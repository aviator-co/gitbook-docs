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

FlexReview is a code review platform focusing on teams. With GitHub `CODEOWNERS`, you can define which user or team should approve a PR. FlexReview pushes this forward more and makes code review team work rather than a mere personal activity.

FlexReview consists of following components:

* [Recursive ownership](concepts/recursive-ownership.md)
  * GitHub `CODEOWNERS`semantics is not expressive enough to capture the actual shape of code ownership. FlexReview provides an alternative way for defining an ownership.
* [Review assignment focusing on teams](concepts/reviewer-suggestion-and-assignment.md)
  * Each team can choose an assignment strategy from code expertise, review load, or oncall rotations.
  * When a PR spans multiple teams, it finds a common parent owner and minimizes number of reviewers.
* Notifications and automations
  * Slack notification can be sent to team's channel when there's new PR for your team.
  * Reviewer can be auto-reassigned when there's no reply.
* [SLO goals for code review](concepts/slo-management.md)
  * Teams can define the expected size of the PRs and review turnaround time.
  * Dashboard shows how many PRs are within the review-time SLO.
* [Team dashboard for incoming PRs](../attentionset/)
  * Members can see the PRs that modify their owned files.
* [Review Validation](concepts/validation-in-flexreview.md)
  * A validation system to ensure that appropriate approvals have been granted before a pull request is considered mergeable.
  * Reviewers are responsible for approving on behalf of the files they own.
  * Reviewers are selectively dismissed when new code changes are pushed based on the changes made.
  * Includes a breakglass override for emergency approvals.

