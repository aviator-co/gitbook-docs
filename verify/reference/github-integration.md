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
* **Where it shows up:** the PR's "Checks" tab and the merge-readiness summary. With the [Aviator Chrome extension](../../mergequeue/aviator-chrome-extension.md), a [**Verify** tab](../how-to-guides/verify-on-github.md) also appears in the PR tab row for acting on the run from the PR.

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

The check summary links back to the review in Aviator for the full review document.

The check doesn't only move when a run finishes. Waiving a verdict or removing an acceptance criterion recounts the latest run against the criteria still active and updates the check right away, so it can flip to `success` without a new run.

### How a PR gets its review

Verify checks a PR against the review it's linked to. When a PR is opened, edited, or marked ready for review, Aviator looks for that review in this order:

1. **A link in the PR body.** If the body contains a review URL (`<your Aviator URL>/r/123`), the PR links to that review. This only works when the PR's author owns the review. Otherwise Aviator comments on the PR and doesn't link it. `/verify-submit` puts this link at the top of the PR body.
2. **The working branch.** With no link in the body, Aviator looks for the PR author's active reviews in the repo whose working branch matches the PR's branch. If exactly one matches, the PR links to it. If more than one matches, the PR links to none of them.
3. **Auto-create.** If nothing linked and **Auto-create review on PR open** is on for the repo (see [Connect a repository](../how-to-guides/connect-a-repository.md)), Aviator creates a review from the PR. It does this when a PR is opened or marked ready for review, never for a draft and never on an edit. It comments on the PR with the review URL, writes an intent from the PR, and starts the first verification run. The review has no acceptance criteria of its own, so the account's invariants alone gate the PR. Add criteria with [`aviator edit`](cli.md#aviator-edit) or in the review. The PR's author owns the review, so their GitHub account has to be connected to Aviator. If it isn't, no review is created.

Two reviews on one branch break step 2: the PR links to neither, and with auto-create on, Aviator creates a third review from the PR. Before submitting, check for an existing review with `aviator sessions --repo <owner/repo> --branch <branch>`, and keep the review link in the PR body.

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
* [Understanding verification results](../how-to-guides/understanding-verification-results.md)
* [Slack notifications](slack-notifications.md)
