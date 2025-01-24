---
description: >-
  Learn about using GitHub OAuth connection in Aviator and its configuration.
  GitHub OAuth maps a GitHub username to an Aviator user within a single
  installation.
---

# GitHub OAuth for On-Prem

There are two types of GitHub connections that Aviator uses:

* GitHub app - this ensures that Aviator can connect with the GitHub repos and take action on the pull requests. This is typically done as part of initial setup, and there is one shared connection across the entire GitHub organization.
* GitHub OAuth - this is used to map a GitHub username to an Aviator user within a single installation. Every engineer in the team is expected to make this OAuth connection to enable certain features in Aviator. For instance, with this a user can then access their [<mark style="color:blue;">AttentionSet</mark>](https://docs.aviator.co/attentionset), or get [<mark style="color:blue;">personal Slack DMs</mark>](https://docs.aviator.co/mergequeue/how-to-guides/custom-integrations/personal-integrations#personal-slack-notifications).

## On-premise setup

You would typically [<mark style="color:blue;">set up a new GitHub app</mark>](https://docs.aviator.co/manage/on-premise-installation/github-app-for-on-prem) when installing Aviator on-premise. But GitHub OAuth connection is optional.

GitHub OAuth connection can be configured once the GitHub app is set up. To configure the GitHub OAuth connection:

**Step 1**

In the Github app settings, change the Callback URL to: **Callback URL:** `https://$AV_HOSTNAME/login/oauth/callback/github`

**Step 2**

Set the env variables for `GITHUB_CLIENT_ID` and `GITHUB_CLIENT_SECRET` for the Aviator server (both web and worker). Depending on your installation choice, the env variables may be set in different ways.

* `$GITHUB_CLIENT_ID`: Client ID of the GitHub app, you can find this in the General settings of the GitHub app.
* `$GITHUB_CLIENT_SECRET`: Client Secret of the GitHub app, you can generate a client secret in the General settings for the Github app.

**Step 3**

Restart the server.

Now if you navigate to `/attentionset` you should be able to complete a GitHub OAuth workflow.
