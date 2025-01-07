---
icon: code-merge
---

# FlexReview (Beta)

{% hint style="success" %}
Read our launch post about why we built FlexReview: [https://www.aviator.co/blog/flexreview-a-flexible-code-review-framework/](https://www.aviator.co/blog/flexreview-a-flexible-code-review-framework/)
{% endhint %}

## What is FlexReview

FlexReview is a code review platform focusing on teams. With GitHub `CODEOWNERS`, you can define which user or team should approve a PR. FlexReview pushes this forward more and makes code review team work rather than a mere personal activity.

FlexReview consists of following components:

* Define recursive ownership
  * GitHub `CODEOWNERS`semantics is not expressive enough to capture the actual shape of code ownership. FlexReview provides an alternative way for defining an ownership.
* Review assignment focusing on teams
  * Each team can choose an assignment strategy from code expertise, review load, or oncall rotations.
  * When a PR spans multiple teams, it finds a common parent owner and minimizes number of reviewers.
* Notifications and automations for the code review
  * Slack notification can be sent to team's channel when there's new PR for your team.
  * Reviewer can be auto-reassigned when there's no reply.
* SLO goal for code review
  * Teams can define the expected size of the PRs and review turnaround time.
  * Dashboard shows how many PRs are within the review-time SLO.
* Team dashboard for incoming PRs
  * Members can see the PRs that modify their owned files.

