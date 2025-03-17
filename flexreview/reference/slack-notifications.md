---
description: Description of FlexReview Slack notification settings
---

# Slack Notifications

FlexReview is highly configurable, allowing you to define where and how you would like your teams to get notified about incoming reviews and new PRs.

## Notification Content

Each standard notification will include the assigned reviewers to a PR, the PR author, as well as a link to the PR making it easy to access and promptly respond to a review request. Any user who has connected their Aviator account to both GitHub and Slack will receive a single direct at-mention on one of these messages directed to a team they belong to. All other users will show up as their GitHub username. Teams that have FlexReview and Auto Assignment enabled but have Review Assignment set as No Assignment will be mentioned by their team name.

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption><p>Sample notification with teams and at-mention</p></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption><p>Sample notification with GitHub username instead of at-menti</p></figcaption></figure>

## FYI Notifications

FYI notifications are a way for your teams to be aware of PRs that may impact them, without claiming ownership of the code, or committing to reviewing the PRs. FYI notifications will be sent to the team default Slack channel. They'll contain a little note that the message is intended as an FYI, but will otherwise contain the same basic information as a normal PR excluding the assignment information. For PRs where a team would receive both an assignment message as well as an FYI message, only the assignment message will be sent.

To configure FYI notifications you'll need to use the distributed owners files, you can read more about them generally in [#aviator-config.yaml](../concepts/recursive-ownership.md#aviator-config.yaml "mention"). Inside your `aviator-config.yaml` you'll need to add sections for FYI notifications, similar to the owners sections, it'll be formatted as follows:

```yaml
# src/ios/aviator-config.yaml
notify_teams:
  - team: "acme-corp/ios-eng"
  - team: "acme-corp/ios-auth-eng"
    paths:
      - "auth"
```

Same as the owners portion of these files:

* The `paths` field is optional; if omitted, all files in the directory (recursively) will trigger an FYI notification.
* Paths are interpreted relative to the directory containing `aviator-config.yaml`.

## Team Default Slack Channel

This is the default channel that notifications will be sent to, notifications can be configured to go to other channels as well, but many other settings can take advantage of this.

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption><p>Default channel set to #backend-eng</p></figcaption></figure>

## Automation Rules

Automation rules allow you to control more in a more granular way what messages you would like your team/s to receive. These rules can be set to cascade to all descending teams to ensure that higher level policies apply to all the sub-teams. Within these rules you can set up notifications to either the default team Slack channel, a specific Slack channel per rule, or a DM.

Slack notifications can be set up for:

* No review activity within a specific amount of time
* When a new reviewer is assigned

After an original message is posted on a channel, any future updates to that PR (new assignments, etc.) will be attempted to be posted as a threaded response to the original notification. If for some reason we are unable to post into a thread, instead a normal message will be posted to the channel.
