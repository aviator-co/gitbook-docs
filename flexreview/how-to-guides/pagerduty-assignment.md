# PagerDuty assignment

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
