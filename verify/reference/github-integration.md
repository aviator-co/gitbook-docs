# GitHub integration

Reference for how Verify integrates with GitHub.

### GitHub App

Verify uses the Aviator GitHub App for repository access. Install it from the Aviator UI under **Settings → Integrations → GitHub**, or directly from the GitHub Marketplace.

#### Permissions

The app requests the permissions needed to read code, post checks, and surface verification results on PRs:

| Permission           | Access     | Why                                      |
| -------------------- | ---------- | ---------------------------------------- |
| Repository contents  | Read       | Read the diff and source for verification |
| Pull requests        | Read/Write | Surface verification context on PRs       |
| Checks               | Read/Write | Create and update the `aviator/verify` PR check |
| Metadata             | Read       | Basic repository info                     |

If you don't see Verify behaving as expected, the most common cause is that the GitHub App doesn't have access to the repo. Change access in GitHub under **Organization Settings → Installed GitHub Apps → Aviator → Configure**.

### The PR check

Every verification run is mirrored to GitHub as a single PR check.

* **Check name:** `aviator/verify`
* **Where it shows up:** the PR's "Checks" tab and the merge-readiness summary.

#### Check states

The check state tracks the verification run's status:

| Verification run status | GitHub check state | Notes                                                |
| ----------------------- | ------------------ | ---------------------------------------------------- |
| `pending`               | `queued`           | Run is enqueued but hasn't started.                  |
| `in_progress`           | `in_progress`      | Run is executing.                                    |
| `passed`                | `success`          | All criteria passed or were waived.                  |
| `failed`                | `failure`          | At least one criterion failed without a waiver.      |
| `error`                 | `failure`          | The run itself errored — surfaced as a check failure.|
| `deferred`              | `in_progress`      | Waiting for invariant selection before the run starts.|

The check summary links back to the runbook in the Aviator UI for the full review document.

### Branch protection

To require verification before merge, add `aviator/verify` to your repo's branch protection:

1. Repository **Settings → Branches → Add rule** (or edit an existing rule for your protected branch).
2. Enable **Require status checks to pass before merging**.
3. Search for and select **aviator/verify**.

Recommended settings:

```
☑ Require status checks to pass before merging
   ☑ aviator/verify
☑ Require branches to be up to date before merging (optional)
```

See [Configuring branch protection](../how-to-guides/configuring-branch-protection.md) for the step-by-step.

### See also

* [Configuring branch protection](../how-to-guides/configuring-branch-protection.md)
* [Connect a repository](../how-to-guides/connect-a-repository.md)
* [Understanding verification results](understanding-verification-results.md)
