---
description: >-
  Documentation on updating default branch from master to main when using
  StackedPRs CLI.
---

# Default Branch Update Master to Main

If you choose to switch over the default branch, for instance from master to main, a few steps must be taken to update Stacked PRs CLI to handle this change:

### 1. Ensure that local remote HEAD is updated

You can do this with `git remote set-head origin --auto`.

You can read the git man page for more details:\
[https://git-scm.com/docs/git-remote#Documentation/git-remote.txt-emset-headem](https://git-scm.com/docs/git-remote#Documentation/git-remote.txt-emset-headem)



### 2. Modify .git/av/av.db file to replace the parent

In your repository `.git` directory has a file named `.git/av/av.db`. This file is a JSON document. If you open that, you can see sections like this:\


<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

This file is used by the `av` CLI to track each branch's parent. You can change `master` to  `main` or whatever default branch name change you are making for all existing branches. If you are unsure, please take a backup.

\
With these two steps, av should recognize main as master, including existing branches.
