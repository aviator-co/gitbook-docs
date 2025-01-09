# Prometheus endpoint

The web application server and the background worker server can serve the Prometheus endpoint. You can scrape the metrics from these endpoints. This document provides you how to enable it.

## Web application server

By default, the web application server won’t serve the metrics endpoint. You can enable it by updating `values.yaml`:

```bash
common:
  env:
    - name: "SERVE_PROMETHEUS_ENDPOINT"
      value: "true"
```

This makes the `web` Pod to expose `/debug/metrics` along with other endpoints on the main HTTP port. If the server is exposed to the Internet, we highly recommend changing the load balancer / reverse proxy config in front of the app to hide the `/debug/*` path from public.

{% hint style="info" %}
Can I make it serve on a different port?

Right now, due to the internal architecture, this is difficult.
{% endhint %}

{% hint style="info" %}
Can I add a basic auth?

Please let us know on the requirement. The current implementation doesn’t have it.
{% endhint %}

## Background worker server

By default, the background worker server exposes the metrics endpoint. The port is `8491` (specified by `PROMETHEUS_PORT` environment variable in the Helm chart).

## Exposed metrics

Unlike REST APIs or GraphQL APIs, we do not have intention on maintaining the backward compatibility of the metrics. There can be metrics that we add for debugging purpose (e.g. `bug43123_triggered` counter), and you can find such metrics useful in some cases. However, since we don’t have knowledge whether you depend on which metrics, we may just remove such metrics without letting you know.

Practically speaking, we assume that the following metrics would be used by you, and try to make an effort to maintain them.

* flask\_http\_request\_duration\_seconds
* flask\_http\_request\_exceptions\_total
* flask\_http\_request\_total
* github\_api\_call\_latency
* github\_webhook\_latency
* graphql\_requests\_total
* sqlalchemy\_query\_latency\_seconds
  * This is enabled when `ENABLE_DB_METRICS` environment variable is set to `true`
