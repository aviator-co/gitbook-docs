---
description: >-
  Check the main queue modes MergeQueue can be configured to operate in.
  Default, parallel, and no queue modes explained with different features and
  performance.
---

# Queue Modes

MergeQueue can be configured to operate in one of three queue modes, each with different feature and performance tradeoffs. The queue mode can be configured on a per-repository basis.

## Default Mode

Sequential mode is the default queue mode and is suitable for repositories that merge a relatively small number of pull requests per day.

In this mode, when a pull request reaches the top of the queue, it will automatically be updated with the latest commit from the pull request's target branch (typically `master` or `main`) and merged only if it passes all the required checks.

Default mode can only merge one pull request in the time it takes to run the full suite of checks. For example, if the full suite of checks takes 30 minutes to run, then only one pull request can be merged every 30 minutes (for a theoretical maximum of 48 pull requests per day).

## Parallel Mode

Parallel mode is suitable for repositories that merge a large number of pull requests per day, at the expense of potentially higher CI usages.

In this mode, when a pull request enters the queue, a bot pull request is created containing the changes from the pull request, the latest changes from the pull request's target branch, and every previous pull request currently in the queue. If the bot pull request passes all the required checks, then the original pull request is merged.

If a bot pull request corresponding to a previous pull request in the queue fails, a queue reset is triggered and all subsequent pull requests must be re-validated (potentially wasting the CI resources used for the now invalid bot pull requests).

See the [<mark style="color:blue;">parallel mode documentation</mark>](parallel-mode/) for more details.

## No Queue Mode

No queue mode is suitable for repositories that want to use MergeQueue's merge rules and mergeability checks, but do not want to use MergeQueue's queueing functionality.

In this mode, pull requests maybe automatically updated with the latest changes from the **current** target branch before merging and will **not include** any in-flight (in-queue) PRs. MergeQueue cannot guarantee that the target branch checks will be passing after merging in this mode.
