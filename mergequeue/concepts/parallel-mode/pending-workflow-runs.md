---
description: >-
  Learn how to hold a PR out of the queue until GitHub has started the CI runs
  for its latest commit, so the queue does not admit a PR GitHub will refuse to
  merge.
---

# Pending Workflow Runs

GitHub decides whether a required check is satisfied by looking at the workflow run currently responsible for it. A workflow run exists before the check runs it will report do, and in that window GitHub treats those checks as missing and refuses to merge the pull request.

Normally that window lasts a second or two and nobody notices. It becomes a problem when CI is re-triggered by events that do not change the commit. If a workflow lists `labeled`, `unlabeled` or `edited` under its `pull_request` trigger, then adding a label starts a fresh run on a commit whose code is unchanged. Aviator still holds the previous run's results, which are green, so it admits the pull request. GitHub then declines the merge because the new run has not reported yet.

That produces a loop that is hard to read from the PR page: Aviator queues the PR, GitHub refuses, Aviator marks the PR stuck and adds a label, and the label change starts CI again.

Enabling `wait_for_pending_github_workflows` closes the window. Aviator holds the pull request out of the queue while GitHub has a workflow run for the head commit that has not started, and admits it once the runs are reporting.

## Configuration

This property lives under `merge_rules.merge_mode.parallel_mode` in your Aviator configuration file.

### `wait_for_pending_github_workflows`

Hold a pull request out of the queue while GitHub has a workflow run for its latest commit that has not started yet.

| Property | Value |
| -------- | ----- |
| Type     | boolean |
| Default  | `false` |

```yaml
merge_rules:
  labels:
    trigger: "mergequeue"
  merge_mode:
    type: "parallel"
    parallel_mode:
      wait_for_pending_github_workflows: true
```

## When to enable it

Enable it if your workflows re-run on events that do not change the commit. The clearest signal is a `pull_request` trigger that includes `labeled`, `unlabeled` or `edited`:

```yaml
on:
  pull_request:
    types: [opened, synchronize, labeled, unlabeled]
```

If those triggers are not needed, removing them is the better fix, and it is the one that removes the underlying churn rather than working around it. A label change cannot alter what your CI would test, so re-running on it costs CI capacity and creates the window this setting exists to cover. `synchronize` already covers real code changes.

Leave the setting off if your workflows only run on pushes. It has no effect there, since the runs from the original push have long since reported.

## While a pull request is held

The pull request stays out of the queue and its Aviator status reads `waiting for GitHub to start the CI runs for the latest commit`. The [sticky comment](../sticky-comments.md) names the workflows that have not started, so you can find them on the repository's Actions tab.

Aviator re-checks on its own and queues the pull request as soon as the runs start reporting. No action is needed.

A run that never starts does not hold a pull request forever. Aviator stops waiting on a run after an hour and lets the pull request through, on the grounds that a run which has not started by then is stuck rather than slow. Self-hosted installations can change that limit with the `PENDING_GITHUB_WORKFLOW_MAX_WAIT_MINS` environment variable.

## Limitations

* Available in parallel mode only.
* Applies to GitHub Actions workflow runs. CI providers that report through other means are not covered.
* Aviator does not distinguish runs that feed a required check from those that do not, so a workflow that is slow to start delays queueing even when its checks are not required. This is bounded by the maximum wait described above.
