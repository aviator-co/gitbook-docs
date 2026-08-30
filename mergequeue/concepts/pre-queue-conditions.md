---
description: >-
  Learn what pre-queue conditions you can set to make a PR meet them before
  entering a queue. Number of approvals, required GitHub checks, and others.
---

# Pre-Queue Conditions

You can set up the conditions that a PR must meet before entering a queue. You can configure following pre-queue conditions:

* Number of approvals
* Required GitHub checks
* Pass the GitHub's mergeability
* Require all conversations to be resolved
* Custom regexp checks on the PR title and body

Without meeting these conditions, a PR cannot go into a queue.

In parallel mode you can additionally hold a PR back until GitHub has started the CI runs for its latest commit. See [Pending Workflow Runs](parallel-mode/pending-workflow-runs.md).
