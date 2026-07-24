---
description: >-
  Get started with MergeQueue smoothly using our guide. It's the recommended
  point to begin your journey. We will walk you through the initial setup steps.
---

# Getting Started

This setup guide will walk you through the initial set up for MergeQueue. Since most capabilities of Aviator revolve around merging, we recommend you to start here.&#x20;

1\. Create an account. [<mark style="color:blue;">Register here</mark>](https://app.aviator.co/auth/login).

2\. When you create your account, Aviator automatically walks you through the onboarding flow to connect the Aviator GitHub app and authorize one or more repositories that you want to automate.

{% hint style="info" %}
Already have an Aviator account and just want to add a new repository? See [<mark style="color:blue;">Add a repository to an existing account</mark>](#add-a-repository-to-an-existing-account) below.
{% endhint %}

{% hint style="info" %}
If you have trouble connecting the app, please read the [<mark style="color:blue;">troubleshooting doc</mark>](../manage/faqs/troubleshooting-github-app-connection.md).
{% endhint %}

3\. Select the repository on the [<mark style="color:blue;">**Repositories** page</mark>](https://app.aviator.co/repos) to configure the rules. This will take you to the Basic Configuration page to customize some basic settings.

![Configure a repository.](<../.gitbook/assets/Screen Shot 2022-05-23 at 2.38.56 PM.png>)

4\. On this page, you will see two GitHub labels. These labels are the most common ways for Aviator MergeQueue to interact with your pull requests. The default trigger label is called **mergequeue**.

![Add a trigger label that tells Aviator your PR is ready to be merged.](<../.gitbook/assets/Screen Shot 2023-10-12 at 2.30.13 PM.png>)

5\. Verify the required status checks are set correctly. By default, Aviator syncs the required checks from GitHub branch protection rules, but you can customize them here.&#x20;

<figure><img src="../.gitbook/assets/Screen Shot 2023-10-12 at 2.32.25 PM.png" alt=""><figcaption><p>Required checks on Merge Rules page</p></figcaption></figure>

<figure><img src="../.gitbook/assets/required_checks.png" alt=""><figcaption><p>required check configuration from UI</p></figcaption></figure>

{% hint style="info" %}
If your team is using Parallel mode, Aviator bot will use the same checks for both original PRs and draft PRs. However, if you want to customize checks for draft PRs, you can do so under `Override required checks` in [<mark style="color:blue;">the Parallel Mode section</mark>](concepts/parallel-mode/).
{% endhint %}

***

That's it. You are ready to go. Now when a PR is ready, tag the PR with the trigger label **mergequeue** defined above. Let the Aviator bot handle verifying and merging the PR.

![Simply add the trigger label and your PR will be queued and merged.](<../.gitbook/assets/Screen Shot 2022-05-23 at 5.37.18 PM.png>)

## Add a repository to an existing account

If you already have an Aviator account with connected repositories and want to automate a new one, you don't need to go through onboarding again.

1\. Go to the [<mark style="color:blue;">**Repositories** page</mark>](https://app.aviator.co/repos).

2\. Click **Add Repos**. This opens the Aviator GitHub app installation so you can grant access to the new repository. As a GitHub organization admin, add the repository to the installation.

3\. Back on the **Repositories** page, click **Refetch Repos** so the newly authorized repository appears in your list.

4\. Select the repository and configure it following steps 3–5 above.
