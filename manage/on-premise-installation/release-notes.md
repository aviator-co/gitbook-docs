---
description: >-
  Changelog for Aviator on-premise releases. Subscribe via RSS to get notified
  of new releases.
hidden: true
---

# Release Notes

{% updates format="full" %}

{% update date="2026-03-20" tags="MergeQueue, Runbooks, FlexReview" %}
## 2026.03.20-1-rc1

megacontainer: `sha256:a1b2c3d4e5f6a1b2c3d4e5f6a1b2c3d4e5f6a1b2c3d4e5f6a1b2c3d4e5f6a1b2`<br>frontend: `sha256:b2c3d4e5f6a1b2c3d4e5f6a1b2c3d4e5f6a1b2c3d4e5f6a1b2c3d4e5f6a1b2c3`<br>mergeit: `sha256:c3d4e5f6a1b2c3d4e5f6a1b2c3d4e5f6a1b2c3d4e5f6a1b2c3d4e5f6a1b2c3d4`

**MergeQueue**

* Handle dependent PRs during dequeue events
* Fix dedup logic for concurrent cancelled CI jobs
* Deduplicate blocked PR Slack notifications

**Runbooks**

* REST API for programmatic runbook access
* Failed steps now show "Retry" instead of "Execute Next" to prevent accidentally skipping past failures
* UI improvements: hover cards, combined details/PR tabs, content panel loading

**FlexReview**

* Deduplicate CODEOWNERS gapfill assignments by team
{% endupdate %}

{% update date="2026-03-10" tags="MergeQueue, Runbooks, Releases" %}
## 2026.03.10-2-rc1

megacontainer: `sha256:d4e5f6a1b2c3d4e5f6a1b2c3d4e5f6a1b2c3d4e5f6a1b2c3d4e5f6a1b2c3d4e5`<br>frontend: `sha256:e5f6a1b2c3d4e5f6a1b2c3d4e5f6a1b2c3d4e5f6a1b2c3d4e5f6a1b2c3d4e5f6`<br>mergeit: `sha256:f6a1b2c3d4e5f6a1b2c3d4e5f6a1b2c3d4e5f6a1b2c3d4e5f6a1b2c3d4e5f6a1`

**MergeQueue**

* Reset CI retry counter after successful rework
* Fix parallel mode for forked repository PRs
* Improved error messages for merge conflicts

**Runbooks**

* Trigger Runbooks from Linear
* Improved error handling and observability
* Expand all steps by default in template views

**Releases**

* Improved sync performance for non-released PRs
* Support custom environment ordering
{% endupdate %}

{% update date="2026-02-28" tags="MergeQueue, FlexReview" %}
## 2026.02.28-1-rc1

megacontainer: `sha256:1a2b3c4d5e6f1a2b3c4d5e6f1a2b3c4d5e6f1a2b3c4d5e6f1a2b3c4d5e6f1a2b`<br>frontend: `sha256:2b3c4d5e6f1a2b3c4d5e6f1a2b3c4d5e6f1a2b3c4d5e6f1a2b3c4d5e6f1a2b3c`<br>mergeit: `sha256:3c4d5e6f1a2b3c4d5e6f1a2b3c4d5e6f1a2b3c4d5e6f1a2b3c4d5e6f1a2b3c4d`

**MergeQueue**

* Use non-blocking lock for queue processing to prevent convoy effects
* Check database status before running management commands
* Add visual warning for pending database migrations

**FlexReview**

* PagerDuty integration for excluding on-call reviewers
* Fix reviewer assignment when team members overlap across rules
{% endupdate %}

{% endupdates %}
