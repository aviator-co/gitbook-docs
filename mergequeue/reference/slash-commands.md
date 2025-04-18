---
description: >-
  Discover the GitHub slash commands you can use with Aviator. Merge, cancel,
  refresh, backport, stack merge, stack cancel, sync, and other core commands.
---

# GitHub Slash Commands

{% hint style="info" %}
Read the how-to-guide on [<mark style="color:blue;">using Slash commands</mark>](https://docs.aviator.co/mergequeue/how-to-guides/slash-commands) from within GitHub comments.
{% endhint %}

## Merge

The `/aviator merge` command queues a PR for merging. This can be used instead of adding the ready label. You can also specify affected targets as an additional parameter. See [<mark style="color:blue;">affected targets</mark>](../concepts/affected-targets/) mode to learn more.&#x20;

```
/aviator merge --targets=frontend,backend,api
```

This command can also be used to merge stacked PRs. When queueing a stack, MergeQueue internally queues the PR where the command was given and every PR that comes before it in the stack. The target branch of the stack (i.e., the branch where that the stack is being merged into) is the base branch of the first PR in the stack. Read the details about [merging stacked PRs](../how-to-guides/merging-stacked-prs.md).

{% hint style="info" %}
When merging a stack, any PRs that come later in the stack (after the PR where the stack merge command was issued) will have to be rebased using the `av` command line tool.

For example, if your stack consists of PR1 through PR5, and PR3 is queued, PR4 and PR5 will have to be rebased on top of the commit where PR3 was merged into the stack's target branch (e.g., `main`). This means that PR3 must be merged before PR4 or PR5 can be queued.
{% endhint %}

### Stack merge (deprecated)

The `/aviator stack merge` command can be used to queue a stack for merging into the target branch of the stack (usually your repository default branch).&#x20;

{% hint style="info" %}
This is now deprecated, please use `/aviator merge` for merging the stacked PRs.
{% endhint %}

## Cancel

The `/aviator cancel` command de-queues a PR that has been previously queued.

{% hint style="warning" %}
When using the parallel queue mode, de-queuing a PR can negatively impact the performance of the queue (since any PR that was queued after the cancelled PR will have their CI reset).
{% endhint %}

### Stack Cancel (deprecated)

The `aviator stack cancel` command can be used to de-queue a stack that has been previously queued.

{% hint style="info" %}
This is now now deprecated, please use `/aviator cancel`  for canceling a stack merge.
{% endhint %}

## Refresh

The `/aviator refresh` command causes MergeQueue to re-examine your pull request. This can be useful if MergeQueue missed an event (such as you labeling your PR with the ready label).

Usually this is only necessary if GitHub fails to deliver an event to MergeQueue (e.g., during a GitHub outage).

## Backport

The `/aviator backport` command can be used to backport a given PR to the specified target branch. This opens a new PR that has the cherry-picked changes from the current PR but targeting the specified base branch.

```
/aviator backport <target_branch>
```

## Sync

The `/aviator sync` command synchronizes the PR to be up-to-date with its base branch (i.e., creates a merge commit or rebases on top of the latest commit from the base branch, depending on your repository configuration).

## Blocking

The `/aviator block until` command will block a PR from being merged until a certain block condition is met. Aviator current supports blocking by PR (example: `\aviator block until #382)` and blocking by timestamp (example: `\aviator block until Monday 9pm`). For more information, view the [how-to guide](https://app.gitbook.com/o/RHS3UXVvKc7g6MUrTeGU/s/OAPqUQVbLbsfI5YESl32/~/changes/367/mergequeue/how-to-guides/how-to-block-pull-request-mergeing-with-slash-commands).
