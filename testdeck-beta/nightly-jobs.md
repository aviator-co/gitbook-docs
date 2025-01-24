---
description: >-
  Read about nightly jobs in Aviator and get configuration instructions in our
  guide. These are scheduled job runs that can be triggered with Pilot
  framework.
---

# Nightly Jobs

The nightly jobs are scheduled job runs that can be triggered using the [<mark style="color:blue;">Pilot framework</mark>](https://docs.aviator.co/reference/pilot-automated-actions). These jobs can be used to identify flakes by running the same test on an existing green build. Nightly jobs are also configured using the YAML config file:

```yaml
scenarios:
- name: "nightly build"
  trigger:
    schedule:
      cron_utc: "0 12 * * *"  # Every day at 12pm UTC
  actions:
  - test_suite:
      rerun:
        name: "buildkite/pytest"
        count: 5
        use_latest_green_commit: true
        branch: "master"
```

Note that the `cron_utc` config ignores the minute level precision. You can run it at most at an hourly cadence.
