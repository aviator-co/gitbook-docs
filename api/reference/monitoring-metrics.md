---
description: >-
  Aviator provides monitoring metrics for Prometheus. Monitor your queue length
  and GitHub API usage.
---

# Monitoring Metrics

Aviator provides some monitoring metrics for Prometheus. You can monitor your queue length and GitHub API usage with this.

## Endpoint

`https://app.aviator.co/api/metrics` serves Prometheus metrics. This is authed with your Aviator API key.

In Prometheus config, you can add a scrape config like this:

```yaml
scrape_configs:
  - job_name: "aviator-mergequeue"
    metrics_path: "/api/metrics"
    scheme: "https"
    params:
      # REQUIRED: repository names to expose
      repos:
        - "octocat/Hello-World"
        - "octocat/Spoon-Knife"
      # OPTIONAL: target branch names of the PRs (the merge destination).
      # If it's not specified, grab all PRs.
      branches:
        - "main"
    authorization:
      type: "Bearer"
      credentials: "mq_live_... YOUR API KEY HERE"
    static_configs:
      - targets:
        - "app.aviator.co"
```

You need to update the credentials part and the repository list part.

## Metrics

This endpoint exports following metrics.

<table><thead><tr><th width="292">Name</th><th width="84">Type</th><th width="79">Labels</th><th>Description</th></tr></thead><tbody><tr><td>mergequeue_queued_pr_count</td><td>Gauge</td><td>repo</td><td>Number of queued pull requests</td></tr><tr><td>mergequeue_reset_event_count</td><td>Counter</td><td>repo, branch, reset_type</td><td>Number of reset events over time</td></tr><tr><td>mergequeue_github_rest_api_quota_remaining_count</td><td>Gauge</td><td></td><td>Remaining quota for GitHub REST API</td></tr><tr><td>mergequeue_github_rest_api_quota_limit_count</td><td>Gauge</td><td></td><td>Max quota limit for GitHub REST API</td></tr><tr><td>mergequeue_github_graphql_api_quota_remaining_count</td><td>Gauge</td><td></td><td>Remaining quota for GitHub GraphQL API</td></tr><tr><td>mergequeue_github_graphql_api_quota_limit_count</td><td>Gauge</td><td></td><td>Max quota limit for GitHub GraphQL API</td></tr></tbody></table>

## Merge timing and activity metrics

These describe how long pull requests take to get through the queue and what the queue did with them: batches, merges, blocks and batch closures. These are not enabled by default. Please contact **support@aviator.co** to turn them on for your account; until then the endpoint serves only the metrics above.

<table><thead><tr><th width="292">Name</th><th width="98">Type</th><th width="150">Labels</th><th>Description</th></tr></thead><tbody><tr><td>mergequeue_time_to_batch_seconds</td><td>Histogram</td><td>repo, branch</td><td>Seconds from queue entry to batch creation</td></tr><tr><td>mergequeue_time_to_merge_seconds</td><td>Histogram</td><td>repo, branch</td><td>Seconds from the final queue entry to merge</td></tr><tr><td>mergequeue_cumulative_queue_seconds</td><td>Histogram</td><td>repo, branch</td><td>Seconds spent in the queue across every attempt</td></tr><tr><td>mergequeue_base_updates</td><td>Histogram</td><td>repo, branch</td><td>Base branch updates applied to a pull request before it merged</td></tr><tr><td>mergequeue_pr_wait_seconds</td><td>Histogram</td><td>repo, branch, phase</td><td>Seconds a merged pull request waited before entering the queue (<code>phase="awaiting_enqueue"</code>) and from entering the queue to its merge (<code>phase="in_queue"</code>)</td></tr><tr><td>mergequeue_merged_pr_count</td><td>Counter</td><td>repo, branch, merged_by</td><td>Number of merged pull requests, split by whether the queue merged them</td></tr><tr><td>mergequeue_merge_count</td><td>Counter</td><td>repo, branch, merge_method</td><td>Number of merges performed. All the pull requests merged from one batch count as a single merge</td></tr><tr><td>mergequeue_batch_count</td><td>Counter</td><td>repo, branch, bisected</td><td>Number of batches created, split by whether they are bisected batches</td></tr><tr><td>mergequeue_batch_size</td><td>Histogram</td><td>repo, branch</td><td>Number of pull requests in a batch when it is created, including stacked pull requests it carries</td></tr><tr><td>mergequeue_batch_closed_count</td><td>Counter</td><td>repo, branch, reason</td><td>Number of batches closed without merging, by closure reason</td></tr><tr><td>mergequeue_blocked_pr_count</td><td>Counter</td><td>repo, branch, reason</td><td>Approximate number of times a pull request was blocked, by the status that blocked it</td></tr><tr><td>mergequeue_metrics_rollup_age_seconds</td><td>Gauge</td><td>repo, branch</td><td>Seconds since these metrics were last recomputed</td></tr></tbody></table>

### Label values

* `merge_method`: `mq`, `fast_forward`, `optimistic`, `emergency` or `manual`. `emergency` and `manual` are merges made outside the normal queue flow, so comparing them with the rest shows how much traffic bypasses the queue.
* `bisected`: `true` or `false`.
* `reason` on `mergequeue_batch_closed_count`: `failed_tests`, `ci_timedout`, `pr_dequeued`, `bisection_reshard`, `batch_replaced`, `queue_reset` or `dependency_reset`.
* `reason` on `mergequeue_blocked_pr_count`: the name of the blocking status, for example `MERGE_CONFLICT`, `FAILED_TESTS`, `CI_TIMEDOUT` or `NOT_APPROVED`.

### Merges and merged pull requests

`mergequeue_merged_pr_count` counts pull requests; `mergequeue_merge_count` counts merges. A batch of four pull requests that merges is four merged pull requests but one merge, whether it was fast-forwarded or its pull requests were merged one by one. Merges with no batch, such as emergency merges, manual merges and sequential mode, count once per merge commit.

### Waiting to enter the queue versus waiting in it

`mergequeue_pr_wait_seconds` splits a merged pull request's wait into two phases, measured on the attempt that merged:

* `awaiting_enqueue` runs from when the pull request was marked ready to when it entered the queue. This is time held back by pull request level requirements such as missing approvals.
* `in_queue` runs from entering the queue to the merge. Requeues within the attempt count as time in the queue.

Only pull requests that merged are measured; ones that waited and were then dequeued or blocked are not included.

### Approximate counts

Some of these counts are approximate:

* `mergequeue_blocked_pr_count` undercounts repeated blocks. No block is recorded when a pull request's previous activity was also a block.
* Batches recreated more than once between two refreshes count once in `mergequeue_batch_count`.
* A batch whose merges straddle two refreshes counts as two merges in `mergequeue_merge_count`.
* A pull request the queue merged without recording a merge of its own, such as the lower pull requests of a merged stack, counts in `mergequeue_merged_pr_count` but not in `mergequeue_merge_count`.

### Which duration to use

The three durations answer different questions, and the gaps between them are where the useful signal is.

* `mergequeue_time_to_batch_seconds` is how long a pull request waited for a slot. It grows when the queue is deeper than your build concurrency.
* `mergequeue_time_to_merge_seconds` covers that wait plus the batch's CI run, measured from the pull request's most recent queue entry. Subtracting the first from the second leaves the CI time.
* `mergequeue_cumulative_queue_seconds` adds up every attempt, so it also counts the time spent on attempts that were reset. The gap between it and `mergequeue_time_to_merge_seconds` is time lost to resets.

A pull request re-entering the queue restarts the clock behind the first two, so both describe the attempt that succeeded. Only `mergequeue_cumulative_queue_seconds` spans the earlier ones.

### What to expect on the first scrape

These are computed in the background rather than on the scrape itself, and a repository has nothing cached until it is first scraped. The first scrape after enabling the add-on therefore returns the other metrics but none of these; the next one has them. Values accumulate from that point forward and are not backfilled, so a dashboard covering the first few minutes will look empty.

`mergequeue_metrics_rollup_age_seconds` reports how old the served values are. Under normal conditions it stays under a few minutes; a sustained climb means the values are stale and is worth alerting on.

### Limit on repositories and branches

One request serves at most 100 repository and branch combinations. Past that the extra pairs are dropped, so split large repository lists across several scrape jobs rather than requesting them all at once.

### Note on metric names

You will likely see metrics with some suffixes like `_total` and `_created`. This is Prometheus ecosystem's convention that we need to follow. See [the OpenMetrics format spec](https://github.com/OpenObservability/OpenMetrics/blob/main/specification/OpenMetrics.md) for details.

## See Also

* [How to Collect Prometheus Metrics in GCP](../how-to-collect-monitoring-metrics-in-gcp-prometheus.md)
* [How to Collect Metrics in Datadog](../how-to-collect-monitoring-metrics-in-datadog.md)
