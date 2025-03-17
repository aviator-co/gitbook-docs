---
description: Get advice on setting up your FlexReview Slack notifications.
---

# Recommended Slack Notification Settings

To help your team respond to PRs quickly, these are our recommended settings for Slack notifications in FlexReview. These can be modified to suit your team's needs, but should hopefully provide you a solid starting point.

## Setup

To ensure Slack notifications can be delivered, make sure your admin has enabled Slack integration, if they have not please follow the steps in [#initial-slack-setup](../../api/personal-integrations.md#initial-slack-setup "mention").&#x20;

## Recommended Settings

Because we attempt to deliver only one message to a channel per PR, and only tag users in one message, we recommend being liberal rather than cautious with delivering messages. Towards this goal, we recommend setting up a Team Default Slack Channel for every team. This will allow any cascading rules you set up to take advantage of the default Slack channels for notifications. To set these up go to your `Team Config` page and fill in the Team Default Slack Channel per team:

<figure><img src="../../.gitbook/assets/image (10).png" alt=""><figcaption><p>Team Default Slack Channel</p></figcaption></figure>

We also strongly recommend setting up new reviewer automation rules, these help bring quick awareness to PRs that a team will need to review, the simplest method is setting up a rule as follows:

<figure><img src="../../.gitbook/assets/image (12).png" alt=""><figcaption><p>Basic notification automation rule</p></figcaption></figure>

This will ensure every team receives a new reviewer notification to their default Slack channel any time their team is assigned as a reviewer. If multiple teams share the same Slack channel, it will still only generate one Slack message for that channel.

## Reference

For a detailed description of Slack notification settings you can look at [slack-notifications.md](../reference/slack-notifications.md "mention").
