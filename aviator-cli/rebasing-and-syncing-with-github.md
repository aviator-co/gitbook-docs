---
description: >-
  Learn how to rebase and sync with GitHub. Aviator CLI fetches the latest
  commits from the remote, and rebases your branches on top of the new commits.
---

# Rebasing and Syncing with GitHub

When you work on a stack of branches, the state of the stack can be out of sync with the remote or a parent branch. For example, if the `main` branch on the remote gets updates by merging other pull-requests, or a new commit is added or a commit is amended in one of the parent branches, or some of the parents are merged, you want to rebase your branches on top of the new commits.

`av sync` and `av restack` are commands that do that for you. Aviator CLI fetches the latest commits from the remote, and rebases your branches on top of the new commits. There are some variants of the sync operations depending on your needs.

## Normal Sync

Running `av sync` without any option does the following:

* Handle the merged branches
  * If a branch is merged, its children branches are reparented to the trunk branch.
  * Those reparented branches are rebased on top of the latest trunk branch.
  * You are asked to delete those merged branches in the command.
* Rebase the out-of-sync branches
  * If a parent is amended or a new commit is added, the children branches are rebased on top of the new commit.
  * This is done only for the branches whose parent is not trunk.
* Push the changes to the remote if there's a remote branch.

This is the sync operation you want to use most of the time.

{% hint style="info" %}
Note that the sync operation without any option rebases branches to the trunk branch only if the stack is partially merged (i.e. branch parent is merged). Otherwise, it won't rebase to the trunk branch. This should avoid triggering unnecessary CI/CD pipelines by frequently rebasing to the moving trunk.
{% endhint %}

## Sync with Trunk

Running `av sync --rebase-to-trunk` does the same as the normal sync but rebase the branches to the latest commit of the trunk branch. This means that the CI/CD pipelines are likely to be triggered for the branches.

This is used when you have a merge conflict with the trunk branch (i.e., the file that you modify in your topic branch is modified in the trunk branch), or you want to make sure that your topic branch is up-to-date with the latest trunk.

## Sync without Fetching

Running `av restack` rebases the children branches that are out-of-sync. This won't interact with GitHub.

This is used when you amended a parent branch or added a new commit to a parent branch, and you want to rebase the children branches. Though, if you don't mind fetching and pushing to GitHub, you can use `av sync` instead. There's a convenient commands like `av commit --amend` and `av commit` that does `git commit --amend` and `git commit` followed by `av restack` automatically if you want this behavior.

## Recap

* Use `av sync` most of the time.
* Use `av sync --rebase-to-trunk` when you have a merge conflict with the trunk branch or you want to make sure that your topic branch is up-to-date with the latest trunk.
* Use `av restack` when you want to rebase the children branches without syncing with GitHub.
