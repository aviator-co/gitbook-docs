# Registering an external preview

Some teams already deploy an ephemeral environment for every pull request from their own CI. An **endpoint preview** lets Verify use that environment instead of building one: your pipeline deploys the app as it always has, then registers the resulting URL with Aviator, and verification runs against it.

Aviator never builds or tears down an endpoint preview. It correlates the registration to the verification session, waits for it when a run needs it, drives the app at that URL, and stops using it when the registration expires.

For the alternative — Aviator booting a container from a preview image — see [Creating a preview](creating-a-preview.md). For the concept, see [Concepts: Previews](../concepts/previews.md).

**Time:** ~20 minutes

**Prerequisites:**

* The repo connected to Aviator — see [Connect a repository](connect-a-repository.md)
* A CI pipeline that already deploys a reachable per-PR environment
* An Aviator API token — see [API authentication](../../api/reference/authentication.md)

### Step 1: Declare an endpoint preview

Endpoint previews are configured in your **Aviator Verify settings** (**Verify → Settings → Verify**, with the repo selected). Add a `preview` entry with `method: endpoint`:

```yaml
verify:
  preview:
    - name: ci
      method: endpoint
      wait_timeout_sec: 1800     # how long a run waits for a registration
      expires_default_sec: 7200  # registration lifetime when a registration omits expires_at
```

Endpoint entries take none of the sandbox fields (`image`, `port`, `setup`, `teardown`, `secrets`) — the environment isn't Aviator's to build. See [Preview YAML reference](../reference/preview-yaml.md) for the full schema.

Registrations are rejected until this entry exists: a repo with no `method: endpoint` preview configured returns `400`.

### Step 2: Register the preview from CI

Make the registration the **last step of the deploy job**, after the environment is deployed and reachable. Registering earlier means Verify may start driving a URL that isn't serving yet.

`POST https://api.aviator.co/api/v1/verify/preview-environment`

Authenticate with `Authorization: Bearer <api token>`, the same as the rest of the [REST API](../../api/reference/json-api.md).

#### Request body

| Field                 | Type    | Required | Description                                                                                                              |
| --------------------- | ------- | -------- | ------------------------------------------------------------------------------------------------------------------------ |
| `repository`          | object  | yes      | `{"org": "...", "name": "..."}` — the repository the preview was built from.                                            |
| `branch`              | string  | yes      | The branch the preview was built from. Used to correlate the registration when no PR number is supplied.                 |
| `pr_number`           | integer | no       | The pull request number. Preferred: when present it takes precedence over `branch` for correlation.                     |
| `commit_sha`          | string  | yes      | The commit the environment is running. A registration only drives verification while this matches the head of the branch being verified. |
| `preview_url`         | string  | yes      | Absolute `http(s)` base URL of the live environment. Must be reachable from Aviator.                                     |
| `secrets`             | object  | no       | Credentials for driving the app as a signed-in test user. See [Credentials](#credentials-for-signing-in).                |
| `expires_at`          | string  | no       | ISO 8601 timestamp. When omitted, the config's `expires_default_sec` applies.                                            |

`branch` is required even when you send `pr_number`, because every CI job knows its branch — including jobs that run before a pull request exists.

#### Responses

| Status | Meaning                                                                                                        |
| ------ | -------------------------------------------------------------------------------------------------------------- |
| `202`  | Registration accepted. The body carries `registration_id`, `status`, `url`, `message`, and `expires_at`.        |
| `400`  | Invalid payload — including a repo whose Verify config declares no `method: endpoint` preview.                  |
| `401`  | Missing or invalid API token.                                                                                   |
| `404`  | Repository not found for this account.                                                                          |

A `202` body looks like:

```json
{
  "registration_id": 412,
  "status": "attached",
  "url": "https://app.aviator.co/r/1042",
  "message": "Preview registered and attached to this Verify session.",
  "expires_at": "2026-09-05T18:00:00+00:00"
}
```

`status` tells you what the registration found:

* **`attached`** — a verification session already existed for this pull request or branch, and the registration is now driving it. `url` links to that session.
* **`buffered`** — the registration arrived before the session existed. It's held and attaches automatically once the session is created, so CI outrunning session creation is expected and safe.

Registrations are append-only: re-POSTing records a new registration and the newest one wins, so blind CI retries are safe.

### Step 3: Add the CI step

A GitHub Actions example — adapt the shell and variables to your provider:

```yaml
- name: Register preview with Aviator
  run: |
    curl -sS -X POST https://api.aviator.co/api/v1/verify/preview-environment \
      -H "Authorization: Bearer $AVIATOR_API_TOKEN" \
      -H "Content-Type: application/json" \
      -d @- <<EOF
    {
      "repository": {"org": "acme-corp", "name": "webapp"},
      "branch": "${GITHUB_HEAD_REF}",
      "pr_number": ${{ github.event.pull_request.number }},
      "commit_sha": "${{ github.event.pull_request.head.sha }}",
      "preview_url": "https://pr-${{ github.event.pull_request.number }}.preview.acme.dev",
      "secrets": {"TEST_USER_PASSWORD": "..."}
    }
    EOF
```

Store the token as a CI secret (`AVIATOR_API_TOKEN` above) — never inline it in the workflow file.

### Credentials for signing in

The optional `secrets` object carries the credentials Verify needs to **drive your app as a signed-in user** — a session cookie value, a test user's password. These are for the user agent operating the app, not infrastructure secrets; an endpoint preview is already deployed by the time Aviator sees it, so it never needs your database password or third-party keys.

Your [Verify skill](writing-a-skill-md.md) references them with the same placeholder syntax as account secrets:

```markdown
3. Fill the password field with `{{ secrets.TEST_USER_PASSWORD }}`.
```

The contract:

* **Resolution.** Registered values resolve alongside your account secrets. On a name collision, the registration's value wins for that run.
* **Lifetime.** Values are scoped to the registration — they're deleted when it expires or is deregistered.
* **Handling.** Encrypted at rest, never shown to the verification agent: values are substituted outside the model conversation and scrubbed from captured evidence.

### What a verification run does

When a run reaches work that needs the preview and no current registration exists, it **waits** — the session shows that it's waiting for CI to register a preview — for up to `wait_timeout_sec`.

* **The registration arrives in time.** Verification proceeds against the registered URL.
* **The wait times out.** Verification still completes, using code analysis only; runtime scenarios aren't exercised. This is a degraded result, not a failure — and a registration that lands afterwards starts a follow-up verification automatically.
* **The registration doesn't match the branch head.** A registration whose `commit_sha` isn't the head of the branch being verified never drives the run. The session shows which commit is registered against which commit it's verifying — usually a sign the deploy job is lagging behind a newer push.

Tune `wait_timeout_sec` to your deploy time, with headroom for a queued runner. Too short and runs degrade on a slow build; too long and a broken deploy job holds the run open.

### Re-registering

Registering again is the normal way to move a preview forward:

* **A new commit.** Registering a different `commit_sha` re-verifies it. If that commit is the branch head and was previously verified without a preview, a follow-up verification starts automatically.
* **The same commit at a new URL.** A redeploy — for example, a rebuilt environment on a fresh hostname — makes the new URL current without triggering a re-run.

### Expiry and deregistration

A registration stops driving runs at `expires_at` (or after `expires_default_sec` when the registration didn't set one). At that point its credentials are removed and no new run uses it.

To release it earlier — from a CI teardown job, or when the environment is destroyed on PR close — send a `DELETE` to the same path with the same authentication:

```bash
curl -sS -X DELETE https://api.aviator.co/api/v1/verify/preview-environment \
  -H "Authorization: Bearer $AVIATOR_API_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"repository": {"org": "acme-corp", "name": "webapp"}, "branch": "feature/checkout", "pr_number": 412}'
```

Deregistration is idempotent: an unknown or already-deleted registration returns `200` with `{"deleted": 0}`, so teardown jobs can run unconditionally. Registered credentials are deleted immediately.

### Next steps

* [Preview YAML reference](../reference/preview-yaml.md) — every preview field
* [Writing a Verify skill](writing-a-skill-md.md) — teach the runner how to drive your app
* [Concepts: Previews](../concepts/previews.md) — how previews fit into verification
