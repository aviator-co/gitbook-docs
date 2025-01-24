---
description: >-
  Visit this reference page to view Slack actions for Pilot Automation Actions.
  They allow you to configure sending messages to Slack channels and other
  setups.
---

# Slack

## `slack.channel`

**Using YAML actions:** `slack.channel`

**Using Pilot JavaScript:** `$slack.channel(...)`

Send a Slack message to a channel.

### Inputs

This action action expects an object argument with the following properties:

* `text` (`string`)
  *   The text of the message.

      If `blocks` is provided, then this becomes the text for the Slack notification.
* `blocks` (`Array<{ [key: string]: any }> | null`)
  *   The blocks of the message.

      See https://api.slack.com/block-kit/building for more information.
* `hook_url` (`string | null`)
  *   The webhook to send the Slack message to.

      If not provided, the message will be sent to the default configured channel for the Aviator account.

      See https://api.slack.com/messaging/webhooks for more information.

## `slack.direct`

**Using YAML actions:** `slack.direct`

**Using Pilot JavaScript:** `$slack.direct(...)`

Send a Slack direct message to a user or group of users.

### Inputs

This action action expects an object argument with the following properties:

* `text` (`string`)
  *   The text of the message.

      If `blocks` is provided, then this becomes the text for the Slack notification.
* `blocks` (`Array<{ [key: string]: any }> | null`)
  *   The blocks of the message.

      See https://api.slack.com/block-kit/building for more information.
* `github_users` (`Array<{login: string} > | null`)
  *   The GitHub logins for users that will be messaged.

      These users must connect both their GitHub and Slack accounts to Aviator in order to receive the message.
* `github_group` (`string | null`)
  *   GitHub group whose members will be messaged.

      The group must be a GitHub team in the form `@org/team`.

      The members of the group must connect both their GitHub and Slack accounts to Aviator in order to receive the message.
* `labels` (`Array<string> | null`)
  *   Labels associated with this Slack message.

      Users can opt-in or opt-out of certain labels to personalize their Aviator Slack messages.
* `opt_in` (`boolean | null`)
  *   Whether users must opt-in to receive the Slack message.

      If true, then users must explicitly opt-in to receive the message. Otherwise, all users will receive the message unless they have opted-out.
