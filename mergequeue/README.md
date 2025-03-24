---
description: >-
  Get introduced to MergeQueue to automate merge workflows for GitHub
  repositories. Documentation on how it works.
icon: list-radio
---

# MergeQueue

## Introduction

MergeQueue manages the merge workflow for pull requests in your GitHub repository and keeps your primary branch passing. When a developer marks a pull request as ready-to-merge, MergeQueue tests the pull request against the latest changes in the target branch and merges the pull request only if it passes all the required checks, ensuring no broken code can be merged into your primary branch.

{% embed url="https://www.youtube.com/watch?v=JAuMXp2eu9Q" %}
MergeQueue demo with Parallel mode
{% endembed %}

## Why use a merge queue?

CI tools can run tests on every pull request when it's opened, as well as on every branch after it's pushed, but it may not be sufficient to avoid broken builds.

For example, if two pull requests modify dependent code, checks might pass on each pull request independently, but the build may still break after the merge (even if there's no Git merge conflict).

These kinds of _semantic conflicts_ cause broken code to be merged into the repository's primary branch and negatively impact the productivity of engineering organizations since developers have to spend time fixing the build instead of working on new features (and no new code can be merged in the meantime).

Some organizations configure GitHub to require pull requests be up-to-date with the repository primary branch to avoid this issue, but this is not a scalable solution as teams grow. This configuration means every individual engineer has to:

* Update their pull request to merge the changes from `main`
* Wait for the required checks to pass (again)
* Ideally, merge the pull request
* Less ideally, if another pull request is merged in the meantime, repeat the steps above

## How does it work?

MergeQueue provides several [<mark style="color:blue;">queue modes</mark>](concepts/queue-modes.md) that are optimized for different types of repositories. All modes follow the same basic flow:

1. Aviator monitors all pull requests on your GitHub repository.
2. Instead of manually merging pull requests (_e.g._, using the GitHub merge button), developers add their pull requests to the queue when they are ready to merge (usually by commenting `/aviator merge` or adding a configurable GitHub label to the pull request).
3. Depending on your [<mark style="color:blue;">queue mode</mark>](concepts/queue-modes.md), MergeQueue will test the pull request against the latest changes in the target branch as well as the changes from every other pull request in the queue.
4. When all the required checks pass, MergeQueue will merge the pull request into the target branch without any additional effort from the developer.
5. If something goes wrong (such as a failing required check), MergeQueue reports the error back to the developer and removes the pull request from the queue.

![MergeQueue automatically dequeues PRs and reports build failures.](<../.gitbook/assets/Screen Shot 2022-05-23 at 5.33.58 PM.png>)

Aviator automatically captures both required and non-required the status checks from your repository. By default, Aviator will validate the required status checks configured in GitHub. To learn more about status checks, checkout [<mark style="color:blue;">customizing required checks section</mark>](https://docs.aviator.co/mergequeue/how-to-guides/customize-required-checks).
