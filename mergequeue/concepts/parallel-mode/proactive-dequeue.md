---
description: >-
  Learn how proactive dequeue identifies and removes failing PRs from the middle
  of the parallel mode queue without waiting for them to reach the top.
---

# Proactive Dequeue

In parallel mode, each draft PR includes the cumulative commits of all preceding queued PRs plus its own batch. When a draft PR's CI fails, the failure is normally only acted upon when that draft PR reaches the top of the queue. This means a failing PR in the middle of the queue can delay progress until all preceding PRs are merged first.

Proactive dequeue solves this by using a simple heuristic: if all of a draft PR's dependencies have passing CI but the draft PR itself has failed CI, then the batch of PRs unique to that draft PR likely introduced the failure. Aviator can proactively dequeue those PRs without waiting for them to reach the top.

## Configuration

Both properties live under `merge_rules.merge_mode.parallel_mode` in your Aviator configuration file.

### `use_proactive_dequeue`

Enables proactive dequeue for your repository. When enabled, Aviator scans non-top draft PRs during each queue processing cycle and dequeues any that meet the heuristic criteria.

| Property | Value |
| -------- | ----- |
| Type     | boolean |
| Default  | `false` |

### `proactive_dequeue_delay_minutes`

Number of minutes to wait after a draft PR CI failure is detected before proactively dequeuing. This delay helps avoid false positives from transient or flaky test failures. Only applicable when `use_proactive_dequeue` is `true`. A value of `0` means Aviator will dequeue as soon as the heuristic conditions are met.

| Property | Value |
| -------- | ----- |
| Type     | integer |
| Default  | `0` |

### Example

```yaml
version: 1.0.0
merge_rules:
  labels:
    trigger: "mergequeue"
  merge_mode:
    type: "parallel"
    parallel_mode:
      max_parallel_builds: 10
      batch_size: 1
      use_proactive_dequeue: true
      proactive_dequeue_delay_minutes: 5
```

## How it works

Consider a queue with three draft PRs:

```
Draft PR #1 (contains: PR A, PR B)  →  CI: passing
Draft PR #2 (contains: PR C, PR D)  →  CI: failing
Draft PR #3 (contains: PR E, PR F)  →  CI: pending
```

Draft PR #2 includes commits from PR A and PR B (carried forward from Draft PR #1) plus PR C and PR D (its own batch). Since Draft PR #1 passes with only A and B, but Draft PR #2 fails after adding C and D, the failure is likely caused by PR C or PR D.

With proactive dequeue enabled, Aviator will:

1. Detect that Draft PR #2 has failed CI with all checks complete.
2. Verify that Draft PR #1 (the dependency) has fully passing CI.
3. Wait for the configured delay if `proactive_dequeue_delay_minutes` is set.
4. Dequeue PR C and PR D from the queue.
5. Resync Draft PR #3 to remove the dequeued commits and retrigger CI.

A comment is posted on the affected PRs explaining that they were proactively dequeued and why.

### With batching

When `batch_size` is greater than 1, the proactive dequeue identifies the failing **batch** but cannot pinpoint the exact PR within the batch. In this case, the PRs in the batch are requeued for [bisection](batching.md) to narrow down the culprit, rather than being immediately blocked.

### With affected targets

When `use_affected_targets` is enabled, draft PRs may have multiple dependencies rather than a single linear predecessor. Aviator checks **all** dependent draft PRs in the dependency graph. Every dependency must have fully passing CI before the proactive dequeue heuristic fires.

## When proactive dequeue does not fire

The heuristic is intentionally conservative. No action is taken when:

* **Any dependency still has pending CI.** Aviator waits for all dependent draft PRs to finish CI before making a decision.
* **Any dependency is also failing.** The failure could be cascading from an upstream batch. Aviator lets normal top-of-queue processing handle it.
* **The draft PR still has pending checks.** Aviator waits for all CI checks to complete before evaluating.
* **The draft PR is a bisected batch.** Bisection has its own processing path.
* **The draft PR is at the top of the queue.** Normal queue processing already handles top-of-queue failures.
* **The configured delay has not elapsed.** Aviator re-checks on the next processing cycle.

## Recommendations

* **Start with a delay.** Setting `proactive_dequeue_delay_minutes` to 5-10 minutes gives transient failures time to resolve before Aviator acts. You can reduce it once you are confident in the behavior for your repository.
* **Works best with `batch_size: 1`.** When each draft PR contains a single queued PR, the heuristic can directly identify the failing PR. With larger batch sizes, the failing batch is requeued for bisection, which still saves time but requires additional CI cycles.
* **Complements optimistic validation.** Proactive dequeue identifies failing PRs in the middle of the queue, while `use_optimistic_validation` recovers from flaky failures at the top. Both can be enabled simultaneously without conflict.
