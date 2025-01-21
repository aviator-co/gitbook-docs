---
description: >-
  Check out best practices for AttentionSet to learn its standard operating
  principles for maximum benefits on developer productivity.
---

# Best practices

AttentionSet works well when everyone agrees to some standard principles of operating:

* Defining a rough response time helps the author manage the right expectations without getting to the point of constant reminders, or escalations. You can also define a team-wide SLO separately for an internal and an external code review using the Aviator’s [<mark style="color:blue;">SLO management</mark>](../flexreview/concepts/slo-management.md).
* We recommend an SLO of 1 business day for all PRs that have < 200 lines of change. Google also[ <mark style="color:blue;">recommends a fast code review iteration time</mark>](https://google.github.io/eng-practices/review/reviewer/speed.html) for improved developer productivity.
* If you think that you will not get to the review within the suggested response time window, it’s recommended to remove attention from yourself and inform the author.
* As an author who has the attention, if you change requires a bit of rework before it can be sent back for review, it’s recommended to move the PR to a draft or remove the attention from the reviewers.
* As an author, if you assign an optional reviewer just as an FYI and you do expect a proper review from the reviewer, you should remove attention from that reviewer.
* Likewise, as a reviewer if you are assigned an optional review that you do not intend to get to, you should remove attention from yourself.
