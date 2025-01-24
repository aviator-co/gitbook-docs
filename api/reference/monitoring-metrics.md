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

### Note on metric names

You will likely see metrics with some suffixes like `_total` and `_created`. This is Prometheus ecosystem's convention that we need to follow. See [the OpenMetrics format spec](https://github.com/OpenObservability/OpenMetrics/blob/main/specification/OpenMetrics.md) for details.

## See Also

* [How to Collect Prometheus Metrics in GCP](../how-to-collect-monitoring-metrics-in-gcp-prometheus.md)
