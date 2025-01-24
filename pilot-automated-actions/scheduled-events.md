---
description: >-
  With the Aviator Pilot framework, you can configure rules to trigger actions
  on a specific fixed schedule. Get instructions on configuring a schedule here.
---

# Scheduled Events

Using the Aviator Pilot framework, you can set up rules to trigger actions on a fixed schedule. For instance, you can use schedules to set up a schedule to automatically pause / unpause the MergeQueue, or trigger a nightly job to run your builds

## Configuring schedule

Schedule configuration accepts a [<mark style="color:blue;">unix cron format</mark>](https://www.ibm.com/docs/en/db2/11.5?topic=task-unix-cron-format) that provides enough configurability to set up automated rules. Here’s a quick example that will pause the queue at 11pm UTC.

```
scenarios:
- name: pause the queue
  trigger:
    schedule:
      # pause the queue at 11pm UTC every Friday
      cron_utc: "0 23 * * 5"
  actions:
    - mergequeue: pause
```

Similarly, you can set up a nightly job to trigger a CI to run 5 times to identify any flaky tests.

```
scenarios:
- name: "nightly build"
  trigger:
    schedule:
      cron_utc: "0 12 * * *" # Every day at 12pm UTC
  actions:
    - test_suite:
      rerun:
        name: "buildkite/pytest"
        count: 5
        use_latest_green_commit: true
        branch: "master"

```

{% hint style="info" %}
Currently Pilot framework only support precision of 1 hour, so the events will only be triggered at most once an hour. If you have use cases where you may need more frequent scenarios, please email us at info@aviator.co.
{% endhint %}
