---
description: >-
  Read more about the Timeline tool. It allows you to look at all the current
  and past actions by Aviator and filter activities based on event and base
  branches.
---

# Timeline

### Overview

The Timeline is a great tool for you to look at all the current and past actions taken by Aviator. Within the Timeline page, you can also filter activities based on event and base branches.

![Timeline shows the chronological order of events in the queue.](<../../.gitbook/assets/Screen Shot 2022-05-23 at 2.48.08 PM.png>)

### Events

| Event         | Description                                                                                                                                                                                                                                                                                             |
| ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **OPENED**    | The moment the PR is opened by a developer. This is the official creation time for a PullRequest.                                                                                                                                                                                                       |
| **LABELED**   | When the PR is labeled by a developer. If a PR was unlabeled and labeled again, this will be reported more than once for the same PR.                                                                                                                                                                   |
| **UNLABELED** | When the PR was manually unlabeled by a developer. This may also be represented multiple times if the PR was unlabeled multiple times. Unlabeling also automatically removes the PR from the queue.                                                                                                     |
| **QUEUED**    | When a PR is queued by the Aviator bot. Typically this would be same as the time when the PR was labeled, but in the case where the PR was labeled before approval, the Aviator bot will wait for proper approvals before queuing the PR.                                                               |
| **TOP**       | When a PR reaches the top of the queue. At this point, the Aviator bot will be actively processing the PR. It’s possible for a PR to lose the top position when someone uses the Skip-the-line label in another PR.                                                                                     |
| **MERGED**    | When the Aviator bot merged the PR. This will not capture any merges that were done manually without the Aviator bot.                                                                                                                                                                                   |
| **BLOCKED**   | A PR may get flagged as blocked if it failed to merge. See [<mark style="color:blue;">Error Codes</mark>](../reference/status-codes.md) section for all scenarios where a PR may be flagged as blocked. In all such cases, the PR is dequeued and the Aviator bot moves on to the next PR in the queue. |
