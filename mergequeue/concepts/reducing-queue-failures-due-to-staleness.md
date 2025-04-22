---
description: >-
  When a code review takes too long, by the time the PR is ready to be merged,
  the PR may become stale. Get troubleshooting instructions here.
---

# Reduce Queue Failures From Staleness

When using MergeQueue in parallel mode, one would typically wait for CI on the original PR to finish and then queue the PR. So the workflow looks like:

1. PR is opened with a few commits, and the author requests a review.
2. The reviewer could provide a few comments that may require author to add a few more commits.
3. Eventually the PR is ready to be merged, and the CI is passing, so the PR will enter the queue. In case of a parallel mode, MergeQueue would create a draft PR with the latest mainline and anything ahead of this PR in the queue. Once the CI passes, the PR will be merged.

This typically works fine, but sometimes a code review process can take a long time (maybe the reviewer or the author went on vacation or the change was deprioritized).

In this case, by the time the PR is approved and ready to be merged, it’s possible that the mainline has moved quite far, and this PR has become “stale”. In this scenario, there is much higher likelihood for this PR to fail its CI, causing a queue reset.

<figure><img src="../../.gitbook/assets/Screen Shot 2023-11-30 at 7.02.56 PM.png" alt=""><figcaption></figcaption></figure>

To reduce such queue resets, it’s better to revalidate the PRs if they are really stale. This can now be achieved using the Ready hook in Aviator. The ready hook is defined by creating a file in your GitHub repository at `.aviator/mergequeue/ready.js` and should define a function `ready` that will be called by the [Pilot JavaScript runtime](../../pilot-automated-actions/js-execution.md).

To detect staleness and automatically update these PRs before they enter the queue, you can create the following ready hook:

```javascript
// .aviator/mergequeue/ready.js

// We'll only update the pull request if it's more than 100 commits behind.
const MAX_COMMITS_BEHIND = 100;

function ready() {
  const base = $event.pullRequest.base.ref;
  const head = $event.pullRequest.head.ref;
  const { behindBy } = $github.compareCommits({ base, head });
  if (behindBy > MAX_COMMITS_BEHIND) {
    $github.addComment(
      `Pull request is ${behindBy} commits behind \`${base}\`. ` +
      `Synchronizing pull request with latest commits before queuing.`
    );

    // Update the pull request with the latest commit from its base branch using
    // the repository's configured strategy (rebase or merge).
    $mergequeue.synchronizePullRequest();
  }
}
```

Now every time a PR is labeled ready to queue, this JavaScript method will ensure that the PR is updated if it’s falling behind. Once updated, the PR will then get automatically queued, so no further action is needed by the developer.
