---
description: >-
  Learn how to setup Prometheus Metrics collection for GCP, with Open Telemetry
  collector running on GKE, to scrape and feed the Aviator Prometheus metrics.
---

# How to Collect Monitoring Metrics in GCP Prometheus

This how-to guide explains how to setup [<mark style="color:blue;">Prometheus Metrics collection</mark>](reference/monitoring-metrics.md) for GCP. We are going to use Open Telemetry collector running on GKE to scrape and feed the Aviator Prometheus metrics.

## Pre-requisite

* GKE cluster

## Step 1: Create a service account and setup permissions

We are going to create a GCP service account that has necessary permission to write metrics. This service account is used via GKE's workload identify federation.

Create a GCP SA `metric-collector@YOUR_PROJECT.iam.gserviceaccount.com` and give it `roles/monitoring.metricWriter` and `roles/monitoring.viewer` permission at the project level.

On the service account permission, give Kubernetes Service Account to use that GCP SA. Give `roles/iam.workloadIdentityUser` to `serviceAccount:YOUR_PROJECT.svc.id.goog[YOUR_K8S_NAMESPACE/metric-collector`.

## Step 2: Create a Kubernetes secret for otel-collector

Open Telemetry collector requires a config where to scrape the metrics and where to send the metrics. Create the following Kubernetes Secret for the configuration.

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: otel-config
stringData:
  config.yaml: |
    receivers:
      prometheus:
        config:
          scrape_configs:
            - job_name: "aviator-mergequeue"
              metrics_path: "/api/metrics"
              scheme: "https"
              params:
                repos:
                  - "YOUR_ORG/YOUR_REPO"
              authorization:
                type: "Bearer"
                credentials: "mq_live_ ... YOUR API KEY HERE"
              static_configs:
                - targets:
                  - "app.aviator.co"

    processors:
      resourcedetection:
        detectors: [gcp]
        timeout: 10s

      batch:
        send_batch_max_size: 200
        send_batch_size: 200
        timeout: 5s

      memory_limiter:
        check_interval: 1s
        limit_percentage: 65
        spike_limit_percentage: 20

    exporters:
      googlemanagedprometheus:

    service:
      pipelines:
        metrics:
          receivers: [prometheus]
          processors: [batch, memory_limiter, resourcedetection]
          exporters: [googlemanagedprometheus]
```

Update `YOUR_ORG/YOUR_REPO` and `YOUR API KEY HERE` parts in the config. The API key can be obtained from [<mark style="color:blue;">https://app.aviator.co/integrations/api</mark>](https://app.aviator.co/integrations/api). Apply this with `kubectl apply -n YOUR_K8S_NAMESPACE -f FILE`.

## Step 3: Deploy otel-collector

Deploy the open-telemetry collector to your GKE cluster. Apply this config to your namespace.

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: metric-collector
  annotations:
    iam.gke.io/gcp-service-account: metric-collector@YOUR_PROJECT.iam.gserviceaccount.com

---

apiVersion: apps/v1
kind: Deployment
metadata:
  name: otel-collector
spec:
  replicas: 1
  selector:
    matchLabels:
      app: otel-collector
  template:
    metadata:
      labels:
        app: otel-collector
    spec:
      serviceAccountName: metric-collector
      containers:
        - name: otel-collector
          image: otel/opentelemetry-collector-contrib:0.91.0
          args:
            - --config
            - /etc/otel/config.yaml
          resources:
            requests:
              cpu: 250m
              memory: 512Mi
              ephemeral-storage: 10Mi
          securityContext:
            runAsNonRoot: true
            allowPrivilegeEscalation: false
          volumeMounts:
            - mountPath: /etc/otel/
              readOnly: true
              name: otel-config
      volumes:
        - name: otel-config
          secret:
            secretName: otel-config
```

## Step 4: Setup a dashboard

The metrics should appear as a Prometheus Metric. You can setup a dashboard like this to monitor the queue length and GitHub API usage on your account.

<figure><img src="../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>
