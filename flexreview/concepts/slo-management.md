---
description: >-
  Learn more about teams and SLO management in FlexReview. FlexReview Service
  Level Objective standardizes code review for teams to speed up the process.
---

# Teams and SLO Management

With the objective of making code reviews faster, FlexReview SLO (Service Level Objective) standardizes the code review process for teams. With SLO:

* Teams can track SLOs and better understand the bottlenecks in code reviews
* Authors are incentivized to create more reviewable PRs
* Automated actions can be configured when SLOs are missed

## Review SLO

Review SLO is the suggested time it should take the reviewer to respond to a PR. Note that this is NOT the time it takes for a PR to be approved, but rather just getting a **first response**.

Think of the code review SLO as an agreement between the author and the reviewer for a pull request. With this agreement, the author is incentivized to create PRs that falls within the bounds of the SLO agreement, and reviewer are incentivized to stay on top of the PRs.

## Considerations for Review SLO

There are a few things that FlexReview accounts for while calculating the SLO:

* Teams that work in different timezones, or different business hours
* Code reviews can come in various sizes, e.g., a 2 line review vs. a 1000 line change
* Reviews are generally faster within a team vs. when dealing with external teams

## FlexReview SLO

To build a meaningful SLO framework, FlexReview accounts for these factors. You can define code review first response times within the code size limits. For example, a code change with less than 400 lines should be reviewed within 1 business day. It tracks the timezones and business days to get a clear picture.

This incentivizes authors to create PRs that will fall into the SLO agreement (smaller changes), thereby also improving the ability to review the code, and the response times. And also incentivizes the reviewers to respond to PRs in time to avoid missing the timeline.

### Config

We will define the SLO specific config at two levels:

* Company level or the default config
* Overrides at a team level (maintaining level of hierarchy)

Although it is recommended that the companies should strive for a singular config (default config), overrides may be necessary for large companies with mixed working styles.

The config at each level includes:

* Two separate SLOs in hours - one for inter-team reviews and another for intra-team reviews
* Max number of lines of change for the SLO agreement to be valid

The SLO tracking is disabled when the change is larger than the max number of lines.

### Analytics

SLO analytics helps you monitor the last 30 days of PR activity for each repository. You can monitor both internal and external team reviews separately. These SLO calculations are done based on business days. You can track:

* SLO hits / misses by team
* P50, P90, P99 for response times by team breakdown
* Non-eligible PRs - the PRs that exceed maximum modified lines specified in the SLO config

## Teams

FlexReview Teams can be configured to support large engineering organizations with many sub-teams. Since processes and workflows vary across teams, we allow rules to be configured based on GitHub teams.

Teams can:

* Define different methods for selecting a reviewer - based on expertise, round robin, etc.
* Set up automated Slack notifications - for missing reviewers, unattended PRs, etc.
* Automatically reassign reviewers.

### Automated actions

Configure automated actions for your team based on the SLO window to improve code review response times. The rules can be set as default for the org and overridden at team level.

The triggers can be one of:

* When a new reviewer is assigned to a PR
* When there is no review activity after X hours

The actions could be one of:

* Send a Slack notification to a team channel
* Send a Slack DM to the reviewer
* Reassign PR review to someone else

