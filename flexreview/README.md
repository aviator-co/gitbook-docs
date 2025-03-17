---
description: >-
  Learn about Aviator FlexReview, a framework that speeds up code reviews by
  understanding the nuances of every code change and assigning reviewers.
icon: code-merge
---

# FlexReview

{% hint style="success" %}
Read our launch post about why we built FlexReview: [https://www.aviator.co/blog/flexreview-a-flexible-code-review-framework/](https://www.aviator.co/blog/flexreview-a-flexible-code-review-framework/)
{% endhint %}

## What is FlexReview

FlexReview is a modern code review platform focused on on collaborative teams. It acts as a drop-in replacement for GitHub `CODEOWNERS` . FlexReview uses domain expertise and recursive ownership to avoid rigid boundaries of GitHub `CODEOWNERS`.

<figure><img src="../.gitbook/assets/Screenshot 2025-03-16 at 7.55.30 PM.png" alt=""><figcaption></figcaption></figure>

## Components

There are two main components of FlexReview:

* [Reviewer assignment](concepts/reviewer-suggestion-and-assignment.md) - Automatically assign code reviewers for selective (or all) pull requests based on the configured preferences.
* [Review Validation](concepts/validation-in-flexreview.md) - Ensure that appropriate approvals have been granted before a pull request is considered mergeable.

## Concepts

There are a few core concepts that differentiates FlexReview from a rigid GitHub `CODEOWNERS` framework.

* [Domain expertise](reference/expert-scoring-algorithms.md)
  * A score calculated based on past history of authorship and reviewership. This is calculated for each file and each user to ensure FlexReview can assign the right reviewers.
* [Recursive ownership](concepts/recursive-ownership.md)
  * True representation of the actual shape of code ownership to avoid rigidness of GitHub `CODEOWNERS`.
  * Simplified ownership definition using distributed YAML files
* [Reviewers minimization](concepts/reviewer-suggestion-and-assignment.md)
  * When a PR spans multiple teams, it finds a common parent owner and minimizes number of reviewers. One or more reviewers are assigned depending on the code complexity.
  * All reviewer assignments are predictable and governed by rules, so you can stay compliant.
* [Team automations](https://docs.aviator.co/flexreview/how-to-guides/set-up-team-rules)
  * Each team can choose an assignment strategy from domain expertise, review load, or oncall rotations.
  * Slack notification can be sent to team's channel when there's new PR for your team.
  * Reviewer can be auto-reassigned when there's no reply.
* [Team dashboard and SLO goals](concepts/slo-management.md)
  * Team members can see the PRs that are assigned to them, or their entire team.
  * Teams can define the expected size of the PRs and review turnaround time.
  * Dashboard shows how many PRs are within the review-time SLO.
* [Selective Dismissal](concepts/validation-in-flexreview.md)
  * A validation system to ensure that appropriate approvals have been granted before a pull request is considered mergeable.
  * Reviewers are responsible for approving on behalf of the files they own.
  * Reviewers can be selectively dismissed when new code changes are pushed tied to their ownership.
  * Includes a [break-glass](concepts/validation-in-flexreview.md#breakglass-scenarios) override for emergency approvals.

## Basic ownership config

Since FlexReview automatically tracks domain expertise for each, you can simply define a top level ownership such that the code is collectively owned by all of engineering, and FlexReview will automatically assign the right reviewers.

```
* @engineering
```

However, most teams would want to define a bit more fine-grained ownership, where recursive ownership helps provide that flexibility.
