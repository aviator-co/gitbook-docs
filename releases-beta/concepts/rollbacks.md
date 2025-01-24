---
description: >-
  Learn about the rollback feature in Releases. Rollbacks enable you to revert
  to a previous stable version when a newly deployed version has issues.
---

# Rollbacks

Rollback is a mechanism to revert to a previous stable version when issues are detected in a newly deployed version. Rollbacks can be categorized in 3 different buckets:

* **Automatic Rollbacks**: This is system's ability to detect failures or issues in the newly deployed version and revert to the previous stable version without manual intervention.
* **Automated Rollbacks**: Some predefined scripts or processes facilitate a rollback, but they may require some level of manual initiation.
* **Manual Rollbacks**: The rollback has be performed in an adhoc fashion with making a few configuration changes directly into the system.

Aviator enable teams to introduce automatic and automated rollbacks in their workflow by streamlining the release and deploy processes.

## Automated Rollbacks

By splitting up the release process in build and deploy steps, automated rollbacks become as simple as rolling forward. Effectively, using Aviator Releases one deploys all versions of the build in the same fashion This makes the process of rollback very transparent eliminating human errors.
