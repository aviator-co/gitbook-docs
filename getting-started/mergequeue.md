---
description: Automated merge workflow
---

# MergeQueue

## Introduction <a href="#introduction" id="introduction"></a>

MergeQueue is a configurable queue that manages the merge workflow for your Github repository. The Aviator bot uses Github Labels to identify PullRequests (PRs) that are ready to be merged and queues them up. The Aviator bot then pulls the latest base branch for each of those PRs, runs and verifies the required status checks, and then merges the changes.&#x20;

![Dashboard view](<../.gitbook/assets/Screen Shot 2022-05-17 at 9.56.31 PM.png>)

### **Why MergeQueue?**

CI tools can run tests on every pull request when it's opened, as well as on every branch after it's pushed, but it may not be sufficient to avoid broken builds.

For example, if you have two pull requests that modify dependent code, the tests could pass on each pull request independently, but the build may still break after the merge.

You could configure GitHub to block pull requests that are not up-to-date with `master`/`main` to avoid this issue, but that is not a scalable solution as your team grows. This configuration means every individual engineer has to:

* Update current branch with master.
* Wait for the test to pass again.
* Merge the pull request when it's done.
* In case another pull request is merged before that, repeat the steps above.

### How does it work?

Aviator provides several merge modes that you can optimize for your team's repositories. All modes follow the same basic flow:

1. Aviator monitors all pull requests on your Github repository.
2. Instead of manually merging pull requests, the engineers comment `/aviator merge` or add a GitHub label when they are ready to merge their PR.
3. Based on your configurations, Aviator performs the required operations on ready-to-merge PRs.
4. Aviator automatically merges the PR when all the merge criteria has been met.
5. Aviator reports errors and dequeues the PRs that fail the criteria.

![MergeQueue automatically dequeues PRs and reports build failures.](<../.gitbook/assets/Screen Shot 2022-05-23 at 5.33.58 PM.png>)

#### Modes

* Default - The default mode uses a simple FIFO queue. PRs will be merged in the order they are queued. Aviator bot pulls latest changes from mainline to validate CI before merging.
* [<mark style="color:blue;">Parallel mode</mark>](../how-to-guides/parallel-mode/) <mark style="color:blue;"></mark> - CI builds are run optimistically in parallel in order to decrease time-to-merge for high output teams.
* <mark style="color:blue;"></mark>[<mark style="color:blue;">ChangeSets</mark>](changesets/) - PRs can be merged together in a user-defined set.
* [<mark style="color:blue;">Fast Forwarding</mark>](../how-to-guides/fast-forwarding.md) - This works similar to parallel mode, but keeps branch history linear and avoids creating extra commits.

## Status Checks

Aviator automatically captures all the status checks from your repository. To review the status checks you can go to the [<mark style="color:blue;">Merge Rules</mark>](../reference/merge-rules.md) page. Aviator synchronizes the status checks from your GitHub protected branch rules.

On this page, you can select the status checks that the Aviator bot will validate before merging the PR. The bot validates these rules before merging changes in both restricted and non-restricted branches.&#x20;

Please note, for the Aviator bot to be able to merge changes in a restricted branch, all required status checks must be selected on the Status Checks page. The bot might otherwise fail to merge the changes.

{% hint style="info" %}
To further customize your MergeQueue setup, please see [<mark style="color:blue;">Merge Rules</mark>](../reference/merge-rules.md)<mark style="color:blue;">.</mark>
{% endhint %}