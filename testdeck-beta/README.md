---
icon: vial
description: >-
  Read an overview of the TestDeck features. This tool helps teams identify
  flaky tests and take actions to reduce overhead with reporting and
  auto-rerunning.
---

# TestDeck

## Overview

Aviator's TestDeck helps by identifying flaky tests and taking actions to reduce overhead. It has two main capabilities: reporting and auto-rerunning.

A flaky test is a non-deterministic test that passes and fails without any code changes. It causes builds to fail even though the developer hasn’t made any related changes. This requires manual reruns of full CI that result in unnecessary developer overhead and increase in CI compute cost.

{% embed url="https://www.youtube.com/watch?v=2w5gn6SAWRg" %}

### Reporting

Aviator captures and analyzes test results from your preferred CI provider, processes each test case, and tracks performance and reliability of each test case. Here is an overview of capabilities that we offer.

* Identify flaky tests in the system reactively and proactively. These results are reported through our dashboard and using APIs and webhooks.
* Historical view of a particular test case - how often the test has failed (flaky or not), has the test become stable / unstable. Views by feature branches vs. base branches.
* Visibility into whether test stability is degrading or improving for base branches.
* Visibility into whether test run times are increasing or decreasing (analyze P50, P90, etc. of test run times).
* Ability to proactively rerun the test suite at a particular cadence (nightly job) to identify flakes.

### **Auto-rerun unreliable tests**

By using Aviator’s plugins, you can automatically rerun unreliable or flaky tests and control the execution of the tests from a central platform. This significantly improves CI reliability and reduces costs by avoiding multiple reruns. [<mark style="color:blue;">Learn more</mark>](auto-rerun-tests.md).
