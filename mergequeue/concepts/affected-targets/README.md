# Affected Targets

## Overview

The objective of this dynamic queue feature is to provide the benefits of both a multi-queue and parallel processing approach for merging PRs. This is useful for mono-repos where CI runs are long compared to the rate of merge requests. In such cases, basic FIFO queues will never catch up, and a single parallel-mode queue would still force each newly-queued PR to “line up” behind other PRs, even when the PRs ahead of it are entirely orthogonal to it.

### Affected targets in Bazel

This is extremely powerful for build systems like Bazel and Buck, where affected targets invalidated by a change can be computed. For example see [<mark style="color:blue;">bazel-diff</mark>](https://github.com/Tinder/bazel-diff)<mark style="color:blue;">.</mark>

### Other build systems

In other cases, if products are divided by top-level folders, the targets can be the set of top-level folders that cover a change.

## How it works

In this configuration, the mono-repo is separated into logically independent queues. Users signal information to Aviator about which targets are affected by PRs, which allows Aviator merge PRs more aggressively but safely. Namely, when the user indicates that two PRs have no overlapping affected targets, Aviator knows it can test and merge them independently in any order. When targets of multiple PRs do overlap, Aviator optimistically stacks overlapping PRs and tests them together, like it does in parallel mode.

### Implementation

The main effort required of your team is to implement a mechanism to declare which targets are affected by a change. If you need assistance with this configuration, please contact us at [<mark style="color:blue;">howto@aviator.co</mark>](mailto:howto@aviator.co).

### API

Since this approach needs additional information associated with each merge request, it currently works with an API endpoint (instead of the GitHub Labels that our other modes use). The information for affected targets is represented as a JSON array of strings (the strings here are not a predetermined set that needs to be drawn from, and can be dynamically generated), and has an optional custom commit message.

**POST** [<mark style="color:blue;">https://api.aviator.co/api/v1/pull\_request/</mark>](https://mergequeue.com/api/v1/pull\_request/queue)

```json
{
  "action": "update",
  "pull_request": {
    "number": 1234,
    "repository": {
       "name": "repo_name",
       "org": "org_name",
    },
    "head_commit_sha": "69f4404fda48aa2932abfbcb6956a9ccd473b17d",
    "affected_targets": ["targetA", "targetB", …],
    "merge_commit_message": {
      "title": "This is the title. Fixes [TASK-123]",
      "body": "This is where the commit body goes."
    }
  }
}
```

The maximum number of unique “affectedTargets” supported is 1,000,000 per account.

### Example

Let’s say we have a project with 4 targets: A, B, C, and D. It doesn’t matter what they are, but they could, for example, represent 4 artifacts that the project publishes. Now, let’s say we have the following PRs, and we compute the targets they affect (compared to the merge base on the **main** branch):

![](<../../../.gitbook/assets/Screen Shot 2022-05-10 at 1.25.21 PM Medium.jpeg>)

Given the declarations of changed targets above, Aviator can apply different parallel testing and merge strategies that keep unrelated PRs in different test queues.

The main takeaway is that because of the affected targets declarations, there is enormous opportunity to run large portions of in-flight merges in disjoint queues. This allows separate projects or features to be effectively queued independently of each other, unless there truly is a change that impacts multiple targets.

### Other Notes

It’s interesting to note that if every PR declares that it affects the same target(s), it boils down to parallel mode. Conversely, if every PR declares it affects different targets (or no targets at all), it effectively recreates the simple default merge behavior on git. So in a way, an organization’s implementation of how they compute affected targets acts as a dial of how aggressively or conservatively to merge changes.
