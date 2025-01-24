---
icon: slack
description: >-
  Use the Slack integration guide to learn how to set up and customize
  notifications to receive updates about various Aviator events directly in
  Slack.
---

# Slack Integration Guide

With the Slack integration, you can receive notifications about various Aviator events directly in Slack. Follow this guide to set up and customize your notifications.'

## Initial Slack setup

**Admin privileges required.**

To set up the Aviator Slack integration:

1. Navigate to `Settings > Workspace > Integrations`.
2. If you haven't connected Slack yet, click "Connect to Slack" and follow the prompts.
3. Once connected, notifications for Aviator events will start appearing in your selected Slack channel.

<figure><img src="../.gitbook/assets/Screenshot 2024-09-25 at 10.50.06 PM.png" alt="" width="563"><figcaption><p>Initial setup</p></figcaption></figure>

## Customizing the notification channel

**Admin privileges required.**

To change the channel where notifications appear:

1. Create a new Slack channel or select an existing one in your Slack app.
2. Open the channel settings by clicking on the channel name in the Slack header or through the top righ menu and click: `Edit settings`.
3. In the settings, go to `Integrations > Add App`.

<figure><img src="../.gitbook/assets/Screenshot 2024-09-25 at 10.58.53 PM (1).png" alt="" width="375"><figcaption></figcaption></figure>

4. Search for the “Aviator” app and click `Add app` to add it to the selected channel.
5. Return to `Settings > Workspace > Integrations` in Aviator, copy the Slack channel name (without the `#` symbol), replace the old channel name, and save the changes.
6. Now you can test the setup by sending a dummy notification.

<figure><img src="../.gitbook/assets/Screenshot 2024-09-25 at 10.53.57 PM (1).png" alt="" width="563"><figcaption></figcaption></figure>

## Customizing Notifications

Aviator supports two types of notifications: **Channel Notifications** and **Personal Notifications (DMs)**.

### Channel Notifications

**Admin privileges required.**

After setting up the Slack integration, you can choose which event notifications you want to receive in your channel. Some default notifications are already enabled.

<figure><img src="../.gitbook/assets/Screenshot 2024-09-25 at 10.38.51 PM (1).png" alt="" width="563"><figcaption></figcaption></figure>

You can subscribe to notifications for the following events:

* **`MERGEQUEUE_ACTIVATE_DEACTIVATE`** – Notifications when MergeQueue is activated or deactivated.
* **`MERGEQUEUE_CONFIG_UPDATE`** – Notifications when MergeQueue configuration is updated.
* **`MERGEQUEUE_PAUSE_UNPAUSE`** – Notifications when MergeQueue is paused or unpaused.
* **`PR_MERGED`** – Notifications for PRs merged via Aviator MergeQueue.
* **`PR_BLOCKED`** – Notifications for PRs marked as blocked by MergeQueue.
* **`PR_QUEUED`** – Notifications when PRs are queued for merging by Aviator MergeQueue.

### Default Notifications

**Admin privileges required.**

As an admin, you can also set the notifications that all other users will default to. Users can override these settings in the [Personal Notifications](personal-integrations.md#personal-notifications) settings below.

<figure><img src="../.gitbook/assets/F33E0F58-C4C4-41CC-819A-91A8958E0EB8.jpeg" alt=""><figcaption></figcaption></figure>

### Personal Notifications

In addition to the channel notifications, all Aviator users can set up and customize personal notifications (DMs) in Slack. To do this, follow these steps:

1. Open Slack and type `/aviator connect` in any channel, then follow the instructions to link your Slack account to your Aviator user.
2. Go to `Settings > Personal > Integrations` in Aviator. If the Slack link is successful, you’ll see your Slack username with options to customize your personal notifications.

{% hint style="info" %}
You should also associate your GitHub handle with your Aviator user account from the same Integrations page for the Slack notifications to work.
{% endhint %}

<figure><img src="../.gitbook/assets/Screenshot 2024-09-25 at 10.39.02 PM (1).png" alt="" width="563"><figcaption></figcaption></figure>

Similar to channel notifications, you can subscribe to the following personal notification events:

* **`INCOMING_ATTENTION`** – When a PR needs your attention.
* **`PR_BLOCKED`** – When a PR is marked as blocked by MergeQueue.
* **`QUEUE_CONDITION_SATISFIED`** – When a PR meets the queuing condition for MergeQueue (e.g., PR is approved and all checks are passing).
* **`MENTIONED_IN_COMMENT`** – When your GitHub handle is mentioned in a PR comment.
* **`TEST_FAILURE`** – When one of your PRs fails a required check.
* **`RELEASE_DEPLOYED`** – When a new release containing your PR is deployed.
* **`RELEASE_VERIFICATION_REQUIRED`** – When your PR requires verification before deployment.
* **`FLEXREVIEW_REVIEW_ASSIGNED`** – When you are assigned as a reviewer for a PR.
* **`FLEXREVIEW_REVIEW_PINGED`** – Reminder for pending PR reviews, based on your team’s reminder configuration.
