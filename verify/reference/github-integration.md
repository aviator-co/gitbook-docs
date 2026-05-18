# GitHub integration

Technical reference for Verify's GitHub integration.

### GitHub App

Verify uses the Aviator GitHub App for repository access.

#### Permissions

| Permission           | Access     | Purpose                          |
| -------------------- | ---------- | -------------------------------- |
| Repository contents  | Read       | Read code for verification       |
| Pull requests        | Read       | Detect PR events                 |
| Checks               | Read/Write | Create the `aviator/verify` check |
| Metadata             | Read       | Basic repository info            |

#### Installation

The app can be installed with:

* **All repositories** — access to every repo in the org
* **Selected repositories** — access only to chosen repos

Change access in GitHub: **Organization Settings → Installed GitHub Apps → Aviator → Configure**.

### The PR check

#### Check name

```
aviator/verify
```

#### Check statuses

| Aviator run status | GitHub state  | Meaning                              |
| ------------------ | ------------- | ------------------------------------ |
| `PENDING`          | `queued`      | Run created                          |
| `IN_PROGRESS`      | `in_progress` | Verifier running                     |
| `PASSED`           | `success`     | All criteria passed                  |
| `FAILED`           | `failure`     | One or more criteria failed          |
| `ERROR` / `DEFERRED` | `failure`   | Run could not complete               |

#### Check output

The output is a markdown summary that includes:

* Total pass/fail counts
* A per-criterion table: status, criterion text, verifier reason
* A link back to the runbook's verify page for full evidence

### Webhook trigger

Verification runs are triggered automatically on GitHub PR push events (`synchronize`, `opened`) for repos where `enable_verify` is on. Verify de-duplicates concurrent push events for the same head SHA.

There is no manual trigger via PR comments (slash commands) today. Manual trigger is available from the runbook UI in the dashboard.

### Linking a spec to a PR

When a PR is opened, Verify can create a runbook automatically if `auto_create_runbook_on_pr_open` is enabled for the repo. Otherwise, runbooks (and the specs they contain) are created manually in the dashboard.

There is currently no auto-link by branch-name pattern or by PR-description marker.

### Branch protection

To require the `aviator/verify` check before merge, configure GitHub branch protection on the target branch. See [Configuring branch protection](../how-to-guides/configuring-branch-protection.md).

The branch-protection rule is enforced by GitHub. Aviator does not currently track or block merges itself.

### Not yet implemented

The following GitHub-related features are commonly expected and are tracked as roadmap items:

* Slash commands (`/aviator verify`, `/aviator link`, etc.) on PR comments
* Auto-link of specs to PRs via branch name or PR description marker
* PR comments on verification failure (today the GitHub check carries the result; no separate comment is posted)
* Outbound webhooks for verification events (`verification.completed`, `spec.approved`, etc.)

If any of these matter for your team, please share — they are design-partner-shaped.

### See also

* [How to configure branch protection](../how-to-guides/configuring-branch-protection.md)
* [How to connect a repository](../how-to-guides/connect-a-repository.md)
