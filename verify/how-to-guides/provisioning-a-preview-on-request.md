# Provisioning a preview on request

An endpoint preview is usually registered by CI on every push. When a deploy is expensive, or environments come from a limited pool, you may not want one per push — only one per verification. With a `provision` block on the endpoint preview, Aviator asks your system for a preview when a verification run needs one, and tells it when the preview is no longer in use. Your system builds it, registers it, and reclaims it.

This guide covers that round trip. For the registration call itself, see [Registering an external preview](registering-an-external-preview.md); for the concept, see [Concepts: Previews](../concepts/previews.md).

**Time:** ~30 minutes, plus whatever your deploy takes

**Prerequisites:**

* An endpoint preview already declared for the repo — Step 1 of [Registering an external preview](registering-an-external-preview.md)
* Something that can act on a request: a GitHub Actions workflow, or a service that can receive a webhook and deploy a commit
* An Aviator API token for the registration call — see [API authentication](../../api/reference/authentication.md)

### How the round trip works

| Step | Who | What |
| ---- | --- | ---- |
| 1 | Aviator | A verification run reaches work that needs the preview and nothing is registered for its commit. Aviator sends a **`preview_provision`** event and starts waiting. |
| 2 | Your system | Builds and deploys that commit, then registers the URL with `POST /api/v1/verify/preview-environment`. |
| 3 | Aviator | The waiting run resumes and drives the registered URL. |
| 4 | Aviator | When nothing will drive that preview any more, Aviator sends a **`preview_done`** event and retires the commit's registration. |
| 5 | Your system | Tears the environment down, or returns it to the pool. |

Each run asks at most once, and says done at most once. A new push while a run is waiting supersedes that run; the new run asks for the new commit, and the old one is told done.

### Step 1: Add `provision` to the endpoint preview

Pick a transport. Both carry the same event; they differ in where it arrives.

**A GitHub Actions workflow** — Aviator dispatches a `workflow_dispatch` on a workflow in the repo:

```yaml
verify:
  preview:
    - name: ci
      method: endpoint
      wait_timeout_sec: 1800
      provision:
        type: workflow
        workflow: deploy-preview.yml   # under .github/workflows/
        ref: branch                    # or default_branch
```

`ref` chooses which copy of the workflow file runs: `branch` runs the file as it exists on the PR branch, `default_branch` runs the one on the repository's default branch. Use `default_branch` when the deploy workflow is infrastructure you don't want a PR to be able to change.

**A webhook** — Aviator posts to the Verify webhook URL configured for your account under **Settings → Integrations**:

```yaml
verify:
  preview:
    - name: ci
      method: endpoint
      wait_timeout_sec: 1800
      provision:
        type: webhook
```

The `provision` block is the subscription; the webhook URL only has to be set. Events are signed with the signing secret shown on the Integrations page, in the `X-Aviator-Signature-SHA256` header — see [Webhook signatures](../../api/reference/webhooks.md#webhook-signatures).

See [Preview YAML reference](../reference/preview-yaml.md#endpoint-previews-method-endpoint) for every field.

### Step 2: Receive the event

Both events carry the same fields:

| Field | Description |
| ----- | ----------- |
| `event` | `provision` or `done`. |
| `reason` | Why Aviator is done: `run_completed`, `cancelled`, `superseded`, `wait_timed_out`, or `errored`. Empty on a provision. |
| `repository` | The repository, as `{"org": "...", "name": "..."}` (the repository's full name in a workflow input). |
| `branch` | The branch being verified. |
| `pr_number` | The pull request number, when there is one. |
| `commit_sha` | The commit to build. Register this exact value. |
| `session_id`, `session_url` | The verification session asking. Useful in logs and deploy names. |
| `preview_config` | The `name` of the endpoint preview entry. |

**As a webhook**, the body also carries `action`, which is `preview_provision` or `preview_done`. The same webhook URL can carry other Aviator events, so route on `action`:

```json
{
  "action": "preview_provision",
  "event": "provision",
  "reason": null,
  "repository": {"org": "acme-corp", "name": "webapp"},
  "branch": "feature/checkout",
  "pr_number": 412,
  "commit_sha": "3f9c2ab7d1e0c4b5a6f7e8d9c0b1a2f3e4d5c6b7",
  "session_id": 1042,
  "session_url": "https://app.aviator.co/r/1042",
  "preview_config": "ci"
}
```

Respond `2xx` once you have accepted the request, not when the deploy finishes; the registration is how you report completion. A non-`2xx` response, or no response, is recorded on the run as a refused request. It never fails the run — the run waits out `wait_timeout_sec` and then continues on code analysis, exactly as it would without `provision`.

**As a workflow dispatch**, the fields arrive as the workflow's `inputs`, all strings; an empty field is an empty string. Declare the ones you use:

```yaml
# .github/workflows/deploy-preview.yml
on:
  workflow_dispatch:
    inputs:
      event:          { type: string }
      reason:         { type: string }
      repository:     { type: string }
      branch:         { type: string }
      pr_number:      { type: string }
      commit_sha:     { type: string }
      session_url:    { type: string }
      preview_config: { type: string }

jobs:
  preview:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          ref: ${{ inputs.commit_sha }}
      - if: inputs.event == 'provision'
        run: ./scripts/deploy-preview.sh "${{ inputs.commit_sha }}" "${{ inputs.pr_number }}"
      - if: inputs.event == 'done'
        run: ./scripts/destroy-preview.sh "${{ inputs.pr_number }}"
```

The workflow file must declare a `workflow_dispatch` trigger on the ref `provision.ref` names, or GitHub rejects the dispatch; the refusal is shown on the run.

### Step 3: Deploy, then register

Deploy the commit from the event, then make the registration the last step, with the same `repository`, `branch`, `pr_number` and `commit_sha` you were sent:

```bash
curl -sS -X POST https://api.aviator.co/api/v1/verify/preview-environment \
  -H "Authorization: Bearer $AVIATOR_API_TOKEN" \
  -H "Content-Type: application/json" \
  -d @- <<EOF
{
  "repository": {"org": "acme-corp", "name": "webapp"},
  "branch": "feature/checkout",
  "pr_number": 412,
  "commit_sha": "3f9c2ab7d1e0c4b5a6f7e8d9c0b1a2f3e4d5c6b7",
  "preview_url": "https://preview-3.acme.dev",
  "secrets": {"TEST_USER_PASSWORD": "..."},
  "expires_at": "2026-09-05T18:00:00+00:00"
}
EOF
```

The request body, responses and the `secrets` contract are the same as for a CI-registered preview — see [Registering an external preview](registering-an-external-preview.md#request-body). Two things matter more here:

* **`commit_sha` must be the one from the event.** A registration for any other commit never drives the run.
* **Set `expires_at` to cover the verification**, not the deploy. A registration that lapses mid-run leaves the run without a preview. If your environments are reclaimed on a timer, make that timer at least as long as the registration.

### Step 4: Handle `preview_done`

When Aviator sends `done`, no run will use the preview of that commit again, and Aviator has already retired its registration. Tear the environment down or hand it back to your pool. Sending a `DELETE` to the registration endpoint as well is harmless; it is idempotent.

Match the done to the request by `repository`, `pr_number` (or `branch`) and `commit_sha`. A done for a request you never saw, or already closed, is normal — for example, a `wait_timed_out` done after a request your system refused — and should be a no-op.

A run can end before its preview was ever registered. `reason` says which way: `wait_timed_out` means your deploy didn't make it inside `wait_timeout_sec`; `superseded` means a new commit was pushed and a new provision for it follows; `cancelled` and `errored` mean the run stopped for reasons unrelated to the preview.

### What a verification run does

Timing is the whole tradeoff. The run waits up to `wait_timeout_sec` from the moment it asks. Set it to cover:

* the time until your receiver starts (a queued runner, a webhook consumer waking up),
* a cold deploy of the commit, including dependency installs and migrations,
* resetting the environment's state, if you reuse environments (see below),
* one more full cycle, if a request may queue behind another one in progress.

Measure a warm and a cold deploy before choosing. Too short and runs degrade on a slow build; the registration that lands afterwards still starts a follow-up verification automatically, so nothing is lost, but the first result is code-only.

### Reusing a pool of environments

The reason to provision on request is usually that environments are scarce: a few long-lived staging slots, a VM pool, a shared managed database. That shape works well with these events, with a few rules the pattern needs:

* **Queue, don't refuse.** When every environment is busy, accept the request and deploy when one frees up. A refusal degrades the run; a queued request that lands inside `wait_timeout_sec` doesn't.
* **A new push to the same PR replaces its request.** Treat a provision for a PR that already holds an environment as a redeploy in place, not a second claim. Aviator tells you the old commit is `superseded`.
* **Return the environment on `done`, whatever the reason.** A done after a refusal or a timeout is still the signal that nothing will drive that commit; a pool that waits for a "real" completion leaks slots.
* **Reset state on every deploy.** A reused environment carries the previous verification's data unless you reset it. Restore a known snapshot and run the branch's migrations as part of the deploy; see [Seed data for previews](seed-data-for-previews.md#environments-you-host) for what to reset and what to seed from the branch under test.
* **Register credentials that change per deploy; keep fixed ones in account secrets.** If a redeploy mints a new admin password, send it in the registration's `secrets`. If every environment has the same test logins, put those in the account's Verify secrets once and send none.

### Next steps

* [Registering an external preview](registering-an-external-preview.md) — the registration call, credentials, expiry
* [Seed data for previews](seed-data-for-previews.md) — deterministic state, including for environments you host
* [Preview YAML reference](../reference/preview-yaml.md) — every `provision` field
* [Writing a Verify skill](writing-a-skill-md.md) — teach the runner how to drive your app
