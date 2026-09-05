---
description: >-
  Learn how to collect the Aviator Prometheus metrics in Datadog, using the
  Datadog Agent's OpenMetrics check to scrape the endpoint directly.
---

# How to Collect Metrics in Datadog

This how-to guide explains how to feed the [<mark style="color:blue;">Aviator Prometheus metrics</mark>](reference/monitoring-metrics.md) into Datadog. Datadog's OpenMetrics check can scrape a remote HTTPS endpoint directly, so no collector of your own is needed.

## Pre-requisite

* A host or container running the Datadog Agent. The `openmetrics_endpoint` option used below belongs to the current OpenMetrics check; older Agents run a legacy mode configured with `prometheus_url` instead
* An Aviator API key

## Step 1: Configure the OpenMetrics check

Create `openmetrics.d/conf.yaml` in the Agent's `conf.d` directory:

```yaml
init_config:

instances:
  - openmetrics_endpoint: https://app.aviator.co/api/metrics?repos=octocat/Hello-World&branches=main
    namespace: aviator
    metrics:
      - mergequeue_.*
    headers:
      Authorization: "Bearer mq_live_... YOUR API KEY HERE"
    histogram_buckets_as_distributions: true
    min_collection_interval: 60
    tags:
      - repo:octocat/Hello-World
```

You need to update the credentials part and the repository list part.

The repository and branch selection go in the query string here rather than in a `params` block. `repos` is required and can be repeated (`?repos=one&repos=two`); `branches` is optional and covers all branches when omitted.

`openmetrics_endpoint` must be unique per instance, so a second repository set is a second entry in the `instances` list.

## Step 2: Restart the Agent

Restart the Datadog Agent and confirm the check is running:

```bash
sudo datadog-agent status
```

The `openmetrics` section should list your instance with no errors. Metrics appear in Datadog under the `aviator.` prefix, for example `aviator.mergequeue_queued_pr_count`.

## Why histogram_buckets_as_distributions

The merge timing metrics are Prometheus histograms. Without this option the buckets arrive as separate counters and percentiles cannot be computed from them. With it set, Datadog stores them as distributions, so `p50`, `p90` and `p99` are available in queries and monitors, and can be aggregated across repositories and branches.

Set it before building dashboards. Turning it on later changes how the metric is stored, and earlier points are not converted.

## Step 3: Build the dashboard

Useful starting queries:

* Queue depth: `avg:aviator.mergequeue_queued_pr_count{*} by {repo}`
* Time to merge, 90th percentile: `p90:aviator.mergequeue_time_to_merge_seconds{*} by {repo}`
* Time lost to resets: `p50:aviator.mergequeue_cumulative_queue_seconds{*}` minus `p50:aviator.mergequeue_time_to_merge_seconds{*}`
* Merge throughput: `sum:aviator.mergequeue_merged_pr_count.count{*}.as_rate()` by `merged_by`
* Reset rate by cause: `sum:aviator.mergequeue_reset_event_count.count{*}.as_rate() by {reset_type}`

For the reset metric, scrape with an explicit `branches` value. Aviator publishes a zero for every reset type on a named branch, which gives `as_rate()` a floor to work from; without a branch filter, a reset type only appears once it has fired at least once.

## Notes

* Aviator's merge timing values are recomputed in the background, not on each scrape, so a `min_collection_interval` below 60 seconds adds requests without adding resolution.
* A scrape covers at most 100 repository and branch combinations. Split larger estates across several instances.
* Datadog counts each distinct metric and tag combination toward your custom metrics allocation. Scraping many repositories on a short interval is the main thing that drives that number up here.

## See Also

* [Monitoring Metrics reference](reference/monitoring-metrics.md)
* [How to Collect Prometheus Metrics in GCP](how-to-collect-monitoring-metrics-in-gcp-prometheus.md)
