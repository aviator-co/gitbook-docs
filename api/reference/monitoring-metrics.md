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

## Merge timing metrics

These describe how long pull requests take to get through the queue. These are not enabled by default. Please contact **support@aviator.co** to turn them on for your account; until then the endpoint serves only the metrics above.

<table><thead><tr><th width="292">Name</th><th width="98">Type</th><th width="150">Labels</th><th>Description</th></tr></thead><tbody><tr><td>mergequeue_time_to_batch_seconds</td><td>Histogram</td><td>repo, branch</td><td>Seconds from queue entry to batch creation</td></tr><tr><td>mergequeue_time_to_merge_seconds</td><td>Histogram</td><td>repo, branch</td><td>Seconds from the final queue entry to merge</td></tr><tr><td>mergequeue_cumulative_queue_seconds</td><td>Histogram</td><td>repo, branch</td><td>Seconds spent in the queue across every attempt</td></tr><tr><td>mergequeue_base_updates</td><td>Histogram</td><td>repo, branch</td><td>Base branch updates applied to a pull request before it merged</td></tr><tr><td>mergequeue_merged_pr_count</td><td>Counter</td><td>repo, branch, merged_by</td><td>Number of merged pull requests, split by whether the queue merged them</td></tr><tr><td>mergequeue_metrics_rollup_age_seconds</td><td>Gauge</td><td>repo, branch</td><td>Seconds since these timings were last recomputed</td></tr></tbody></table>

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
