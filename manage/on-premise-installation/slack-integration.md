---
description: Slack Notifications for On-prem Installations
---

# Slack Integration

## Create your Slack App

To use Aviator’s Slack notifications, your team will need to set up a Slack app. Follow the steps below.

### Set up app credentials

Navigate to `Basic Information`. Here you will find the information that allows your app to access the Slack API.

You’ll also need to add info for your GitHub app. Find it under `General` > `About` and `Client secrets` at the top of your app page.

Set the following environment variables:

```
SLACK_APP_ID = "..."
SLACK_CLIENT_ID = "..."
SLACK_CLIENT_SECRET = "..."
SLACK_SIGNING_SECRET = "..."

GITHUB_CLIENT_ID = "..."
GITHUB_CLIENT_SECRET = "..."
```

Feel free to set an app icon for Aviator in `Display Information`.

### Add Features and Functionality

Select the following:

* Incoming Webhooks
* Slash Commands
* Bots
* Permissions

<figure><img src="../../.gitbook/assets/Screen Shot 2022-10-25 at 5.23.46 PM.png" alt=""><figcaption><p>Select these functionalities.</p></figcaption></figure>

### OAuth and Permissions

#### Redirect URL

Set up a redirect URL: `https://<your_domain>/api/slack/oauth/finish`

#### Scopes

Select the following **Bot Token Scopes:** `chat:write`, `chat:write.customize`, `commands`, `im:write`, `incoming-webhook`. Please make sure to set these correctly, they are required for the Slack OAuth flow.

<figure><img src="../../.gitbook/assets/Screen Shot 2022-10-25 at 6.41.06 PM.png" alt=""><figcaption><p>Add bot scopes.</p></figcaption></figure>

### Add Slash commands

Create the following slash command, it will link a user’s Slack account with their Aviator activity on GitHub so individuals can receive DMs about their own PRs.

**Command**: `/aviator`

**Request URL**: `https://<your_domain>/slack/slash_command`

**Short Description**: `connect GitHub to your Slack account`

**Usage Hint**: `github connect`

## Connect the App

Follow the instructions here to connect the app to your account and get notifications: [Slack Integration](../../reference/slack-integration.md).
