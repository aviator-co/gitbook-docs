---
description: >-
  With MergeQueue fast-forwarding, Aviator builds parallel speculative pipelines
  in sequential order, guaranteeing that the default branch is always green.
---

# Fast-forwarding

## Overview

Fast-forwarding is a way to linearly move the branch reference from the current head commit to the next commit without creating any additional merge commits. With Aviator’s new fast forwarding feature, we build parallel speculative pipelines in sequential order to achieve this behavior.

## Why fast-forward?

By using this mode, the history in your Git repository remains linear and CI validation on those speculative commits are verified before the default branch is fast forwarded. This guarantees that the default branch is always green.

## How it works

Fast forwarding works by creating temporary branches in a sequential order for every new PR that is ready to merge. Aviator identifies PRs that are ready through GitHub labels.

![](<../../../.gitbook/assets/ezgif.com-gif-maker (1).gif>)

When a PR (`PR #1`) is labeled as ready for merging, Aviator picks the head commit for your base branch (typically `master` or `main`), creates a new branch (`branch A`), and applies all the commits of the labeled PR as a squash commit in `branch A`. Aviator will then validate the CI for this new squash commit. If a second PR (`PR #2`) is labeled while the CI for the first one is still running, Aviator will now pick the latest commit from `branch A` and create a new branch (`branch B`) and apply the commits of the second PR as a squash commit to `branch B`. This way the CI validation can happen in parallel using a speculative commit strategy.

Once the CI for PR #1 passes, Aviator automatically fast forwards the head of your base branch to the head of `branch A`. Likewise if the CI for PR #2 passes, it fast forwards the head of your base branch to the head of `branch B`. By using this strategy, Aviator can both maintain the linear history of your PRs and also ensure that the builds pass on each commit.

If the CI for PR #1 fails, Aviator will not discard `branch A`, and will recreate `branch B` with only the commits of `PR #2`. This way Aviator detects failures before the commits hit the base branch, ensuring that the base branch will always remain green.

![](<../../../.gitbook/assets/ezgif.com-gif-maker (2).gif>)

## Learn more

* [<mark style="color:blue;">How to Set Up Fast-Forwarding</mark>](../../how-to-guides/fast-forwarding.md)
