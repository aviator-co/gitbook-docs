---
description: >-
  Check out the features to be implemented in FlexReview soon and share any
  requests for future releases.
---

# FlexReview Roadmap

## FlexReview Roadmap

There’s a lot to that goes into building an end-to-end code review experience from ground up. With that in mind, we would love to share our near-term roadmap for FlexReview. If you are excited about any of these features, please submit your request in this [<mark style="color:blue;">Google form</mark>](https://forms.gle/x1imtLv64LQ9mewN8). We are prioritizing features based on the interest.

The list below is in the order of priority.

## Chrome Extension :white\_check\_mark:

* See notifications for PRs needing your attention
* See suggested reviewers in GitHub UI
* Track who can review what files in a PR

## SLO timezone tracking

Track author’s and reviewers’ timezones for accurate calculation of SLO requirements.

## Multiple reviewers :white\_check\_mark:

Certain changes / files may need to be reviewed by multiple reviewers before these can be merged. For example, some changes may require the feature team and security team to both sign off.

With multiple reviewers feature you can configure specific file(s) where two or more reviewers must approve for the changes to merge.

This is already implemented, read [Multiple Reviewer Assignment concept](concepts/reviewer-suggestion-and-assignment.md#multiple-reviewer-assignment) for details.

## Coaching mode

Prioritize the new members in the team for review assignment when the changes are authored by expert reviewers.

## Oncall schedules :white\_check\_mark:

Configure code review oncall schedules at the team level. This is currently available for onprem users. Check out [PagerDuty Integration for Reviewers](how-to-guides/pagerduty-integration-for-reviewers.md).

## Distributed config :white\_check\_mark:

Allow Codeowners style override configs to be applied at each directory level instead of one global config.
