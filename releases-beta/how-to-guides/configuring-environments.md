---
description: >-
  Read how to configure requirements in Aviator Releases in our how-to guide.
  Set up a separate environment for anything that can be uniquely deployed.
---

# Configuring Environments

[<mark style="color:blue;">Environments</mark>](../concepts/terminology.md#environment) in Aviator Releases is tied to a Release Project, and there is no correlation of same Environments across different release projects. Set up a separate environment for anything that can be uniquely deployed. Common examples of envinroments are `production`, `staging`, or `development` but it can vary depending on how you manage your environments. For instance, even [<mark style="color:blue;">dogfood, canary or rollouts</mark>](../concepts/dogfood-canary-and-rollout.md) should be configured separately.

To create an environment, go to the configuration page for the Release Project, and click on “Add Environment” button at the bottom right of the page.

<figure><img src="../../.gitbook/assets/Screenshot 2024-07-07 at 9.56.11 AM.png" alt=""><figcaption><p>Create Release Environment config</p></figcaption></figure>

### Environment name

The name of the environment as string. Do not use spaces or special characters other than `-` or `_`. Changing this environment name will also update the all the past deployments associated with this environment.

### Tier

The tier can be `production`, `staging` or `development`. This is only used for internally grouping purposes. Tiered environments are grouped and can be reviewed on the main dashboard for Releases. This property is also mutable.

### Require PR verification before deploy

When enabled, the authors of the PRs are requested to verify their changes (PRs) before those changes can be deployed. If you use a deployment spreadsheet to verify the changes before a deployment, this configuration could easily replace the same with clear audit logs. Learn more about the PR verification workflow.

These PRs can be marked as verified within the Aviator dashboard, and the user can bypass the requirement for any time-sensitive deploys.

### Notify owners after deployment success

If enabled, the authors of the PRs whose changes are in the current release will get notified on Slack that their changes are deployed to the specified environment. This could be a good way to remind authors to verify their changes. For instance, if you would like the PR verification workflow in `production`, you can notify owners after deployment in `staging` as a reminder to verify the changes.

## Deployment configuration

The deployment configuration is similar to the [<mark style="color:blue;">build configuration</mark>](creating-a-release-project.md#build-configuration) in the Release Project.

### Not configured

If your CD service is not supported you can use an API driven workflow, you can choose the deployment step to be “Not configured”. In this configuration, Aviator listens to API calls to manage the deployment status.

### GitHub Actions

<figure><img src="../../.gitbook/assets/Screenshot 2024-07-07 at 10.40.14 AM.png" alt=""><figcaption><p>Deployment with GitHub Actions</p></figcaption></figure>

Since GitHub authorization already provides Aviator access to the GitHub workflows, no additional authorization is needed. Simply select the GitHub workflow that should be triggered for creating a build.

If you do not see your workflow in the dropdown, click on “Fetch workflows” to async fetch the workflow names. Additional workflow parameters can be provided as key-value pairs. These parameters are then passed as is to the GitHub workflow.

{% hint style="info" %}
Note: Please read the full [<mark style="color:blue;">GitHub Actions guide</mark>](working-with-your-ci-cd/github-actions-workflow.md) to configure the GitHub workflow parameters and callbacks to ensure your build and deploy workflows can be tracked in Aviator.
{% endhint %}

### Buildkite

<figure><img src="../../.gitbook/assets/Screenshot 2024-07-07 at 10.40.25 AM.png" alt=""><figcaption><p>Deployment with Buildkite</p></figcaption></figure>

Aviator uses Buildkite [<mark style="color:blue;">API access tokens</mark>](https://buildkite.com/docs/apis/managing-api-tokens) to trigger the Buildkite workflows. Please read the [<mark style="color:blue;">Buildkite setup guide</mark>](working-with-your-ci-cd/buildkite-workflow.md) to understand how to configure the access tokens with the right permissions.

On the Deployment configuration, set the Organization slug and Pipeline slug that will be used to trigger and track the Buildkite pipelines. Please note that these slugs are case sensitive. No callbacks are necessary to track the workflows in Aviator with Buildkite.
