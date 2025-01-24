---
description: >-
  Learn more about the branches and stack in Aviator StackedPRs CLI: Trunk
  branches that PRs are merged into and topic branches that contain proposed
  changes.
---

# Concepts

## Branch and stack

We have two kinds of branches in Aviator CLI:

* Trunk branches: Branches that PRs are merged into. Typically, `main` or `master`.
*   [<mark style="color:blue;">Topic branches</mark>](https://git-scm.com/book/en/v2/Git-Branching-Branching-Workflows#_topic_branch): Branches that contain proposed changes against trunk branches.

    <figure><img src="../.gitbook/assets/Trunk and Topic Branch (1).png" alt=""><figcaption><p>main is the trunk branch, and feature1 and feature2 are topic branches</p></figcaption></figure>

A stack is a series of topic branches. Each branch has one parent branch, and the root of the stack has a trunk branch as a parent. One branch corresponds to one pull-request in GitHub. All pull-requests are ultimately merged into the trunk branch they are rooted. Aviator CLI internally tracks parent-child relationships of branches.

When a parent branch is needed, and it is not specified, it uses the remote `HEAD` as the default trunk branch. This is typically a branch that you set as the default branch on GitHub.

## Sync

<figure><img src="../.gitbook/assets/Sync Branches (2).png" alt=""><figcaption><p>After sync operation, the branches regain the parent-child relationship in their commits.</p></figcaption></figure>

As you add more code to the stack, the state of the stack can be out of sync. For example:

* The `main` branch on the remote gets updates by merging other pull-requests.
* A new commit is added or a commit is amended in one of the parent branches.
* Some of the parents are merged.

In those cases, you want to rebase your branches on top of the new commits. Sync is an action that does that for you. Aviator CLI uses the recorded branch parent to figure out which commit a branch should be rebased onto, and it pushes the new state to GitHub as needed.
