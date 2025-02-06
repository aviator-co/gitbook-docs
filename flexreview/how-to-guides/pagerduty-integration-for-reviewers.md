---
description: >-
  Set up PagerDuty for automated reviewer assignments in FlexReview with this
  step-by-step guide.
---

# PagerDuty Integration for Reviewers

{% hint style="info" %}
This feature is currently available only for on-prem users.
{% endhint %}

Teams can configure their assignment method to use PagerDuty schedule. To use PagerDuty assignment, the on-prem server administrator needs to do one-time setup on the server.

## OnPrem Server Setup

You need to create a PagerDuty App for your Aviator installation. In the Integration menu, you can find App Registration link.&#x20;

<figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

Create a new app with OAuth 2.0 functionality. In the permission settings, check Scoped OAuth and give read access to following 4 resources:

* Escalation Policies
* Oncalls
* Schedules
* Users

You will see the client ID and secret. Set them as the environment variables for the Aviator server:

* PAGERDUTY\_CLIENT\_ID: The client ID of the registered app.
* PAGERDUTY\_CLIENT\_SECRET: The client secret of the registered app.
* PAGERDUTY\_REGION: "us" or "eu". See [https://support.pagerduty.com/main/docs/service-regions](https://support.pagerduty.com/main/docs/service-regions) to check which one your PagerDuty account is in.
* PAGERDUTY\_COMPANY\_SUBDOMAIN: The subdomain part of your PagerDuty page. If you have `https://acmecorp.pagerduty.com`, set `acmecorp`for this variable.

Restart the server to reflect the environment variable changes.

## Assignment Config

Once the server setup is done, you can see the oncall assignment option in the team config page.

<figure><img src="../../.gitbook/assets/image (1) (3).png" alt=""><figcaption></figcaption></figure>

Set the ID of the escalation policy. If your PagerDuty escalation policy page's URL is `https://acmecorp.pagerduty.com/escalation_policies/PCR31GJ` then `PCR31GJ` is the escalation policy ID.

## PagerDuty user to GitHub username association

The Aviator server gets the email address of the oncall. Based on this email address, it looks up the user in the Aviator user database. In order to assign them as a reviewer, each user needs to have their GitHub username registered. To do this, see [slack-integration.md](../../mergequeue/how-to-guides/custom-integrations/slack-integration.md "mention").

We can support batch creation and association of the users. Please contact us if you need this.

## Escalation policies and assignment

PagerDuty's escalation policy can have multiple levels. For example, once an incident happens, alert on the oncall of this schedule. Then after 15 minutes, escalate to this schedule. And so on. FlexReview will roughly follow this rule. It will assign somebody from the first level in the escalation policy. If it cannot find a user to assign, it will check the next level.

&#x20;For example, with the following escalation policy:

<figure><img src="../../.gitbook/assets/2025-02-05 15-26-59.png" alt=""><figcaption></figcaption></figure>

FlexReview tries to assign the oncall of the schedule specified in the level one. If the oncall of that schedule doesn't have a GitHub user associated or if the PR author is that oncall person, it moves on to the next level. The second level has a single user, and it tries to assign that person as a reviewer. It keeps doing this until we find one. If it cannot find any, FlexReview will not assign anybody.

If there are multiple people in the same level, FlexReview will randomly pick one.

## Reviewer exclusion list

PagerDuty assignment rule will ignore the reviewer exclusion list configured at the repository. This is to avoid pull request being dropped without any assignment to the oncall.
