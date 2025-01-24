---
description: >-
  MergeQueue allows you to add more requirements at different levels. Learn
  about pre-queue status requirements and draft PR status requirements
  supported.
---

# CI Status Requirements

Aviator MergeQueue supports any CI system that integrates with [<mark style="color:blue;">GitHub status checks</mark>](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/collaborating-on-repositories-with-code-quality-features/about-status-checks).

In GitHub, you can require tests to be passing with [<mark style="color:blue;">protected branches require status check</mark>](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches/about-protected-branches#require-status-checks-before-merging) before merging. By default, Aviator MergeQueue uses the same data as GitHub status checks, but it allows you to add more requirements at different levels.

* Pre-queue status requirements
  * Same as the GitHub protected branch status requirement, this configuration is the status requirement before merging.
  * MergeQueue requires both GitHub protected branch configuration and this configuration to be satisfied. However, MergeQueue configuration can be more flexible than GitHub one.
* Draft PR status requirements
  * When a pull-request enters a queue, MergeQueue creates a new temporary branch and a PR, which is called "draft PR". This draft PR contains the changes from the queued PR and the latest target branch (e.g. `main`) so that we can test with the latest changes.

You can require the same sets of tests for both, or if needed, you can require different tests to be passing.

## See also

* [<mark style="color:blue;">MQ Created Branches</mark>](optimizing-ci-execution.md) for additional details on the CI setup.
* [<mark style="color:blue;">Pre-Queue Conditions</mark>](pre-queue-conditions.md) for other conditions you can set for the pre-queue time.
* [<mark style="color:blue;">Conditional Checks</mark>](../how-to-guides/customize-required-checks.md#conditional-checks) and [<mark style="color:blue;">wildcards</mark>](../how-to-guides/customize-required-checks.md#using-wildcards) for the flexible configuration options on the test status requirements.
