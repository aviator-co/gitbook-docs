---
description: >-
  Answers to common questions about Aviator plans and pricing, the free trial,
  billed developers, switching or cancelling a plan, payments and invoices,
  Verify run allowances, and Runbooks.
---

# Billing & Pricing FAQs

This page covers Aviator Cloud. Enterprise and self-hosted customers are billed under their contract; see the [last section](#enterprise-and-self-hosted).

You manage your plan, payment method, invoices and billing contacts at **Settings → Workspace → Billing** ([app.aviator.co/settings/workspace/billing](https://app.aviator.co/settings/workspace/billing)). Only workspace admins can see and change billing.

## Plans and pricing

<details>

<summary>What plans does Aviator offer?</summary>

| Plan | Price | What's included |
| --- | --- | --- |
| **Free** | $0 | Dashboard and review Inbox, Stacked PRs CLI, Chrome extension, 10 Verify runs a month shared by the workspace |
| **Team** | $20 per developer per month | Everything in Free, plus MergeQueue, FlexReview and Releases. 10 Verify runs a month shared by the workspace |
| **Scale** | $40 per developer per month | Everything in Team, plus ChangeSets and 50 Verify runs per developer per month |
| **Enterprise** | Custom | Everything in Scale, plus self-hosted deployment, SSO / SAML / SCIM, audit logs, bring-your-own AI key for Verify, and a custom contract |

New workspaces start on a 14-day Team trial. See the full feature comparison at [aviator.co/pricing](https://www.aviator.co/pricing).

</details>

<details>

<summary>How is my bill calculated?</summary>

Team and Scale have one price per **billed developer** per month. That single price covers every product in the plan, so a developer who uses MergeQueue, FlexReview and Releases is billed once, not three times.

For example, a workspace on Team with 25 billed developers pays:

```
25 developers × $20 = $500 per month
```

Aviator dashboard logins and developers who have not used Aviator are never billed. The next question explains exactly who counts.

</details>

<details>

<summary>Is Aviator free for small teams?</summary>

Not for MergeQueue, FlexReview or Releases. Team and Scale are charged from the first billed developer; there is no free allowance of developers.

The Free plan stays free for any team size. It includes the dashboard, review Inbox, Stacked PRs CLI, Chrome extension and Verify, but not MergeQueue, FlexReview or Releases.

</details>

<details>

<summary>What is a billed developer?</summary>

A billed developer is a GitHub user whose pull request activity went through an Aviator product. What counts depends on the product:

* **MergeQueue:** the author of a pull request that was queued for merge, or that MergeQueue updated with the latest base branch.
* **FlexReview:** the author of a pull request that FlexReview assigned reviewers to. The reviewers themselves are not billed. Because FlexReview assigns reviewers automatically, every developer who opens a pull request in a FlexReview-enabled repository becomes billed, even if they never open Aviator.
* **Releases:** the authors of the pull requests included in a release that was deployed through Aviator Releases.
* **Verify:** the developer a Verify run is attributed to: the person who triggered it manually, otherwise the author of the pull request.

A developer who shows up in several products is counted once. The Billing page lists these developers under **Billed users**, with a separate count for each product. On Team and Scale you pay for the unique developers across all products, not the sum of those counts. The **Details** button opens the full list.

</details>

<details>

<summary>What is the difference between a collaborator and a user?</summary>

A collaborator is a GitHub user with access to your organization or repositories. You pay only for collaborators who become billed developers through the activity described above.

An Aviator user is someone who can sign in to the Aviator dashboard. There is no cost for dashboard users on any plan. You can invite as many teammates as you like.

</details>

<details>

<summary>Are bots billed?</summary>

No. GitHub Apps and bot accounts such as Dependabot or Renovate can queue pull requests and use Aviator normally, but they are never billed developers. Only human GitHub users are billed.

</details>

<details>

<summary>Do you offer annual billing?</summary>

Yes. Annual billing is set up by our team rather than from the Billing page. Contact [support@aviator.co](mailto:support@aviator.co) and we will move your subscription to a yearly cycle.

Annual prices are $200 per developer per year on Team and $400 per developer per year on Scale, which is two months free compared to paying monthly. On an annual plan, developers added mid-year are prorated for the remainder of the year.

</details>

<details>

<summary>I'm on the older per-product Pro plan. How does my billing work?</summary>

Workspaces that subscribed before the Team and Scale plans were introduced stay on per-product pricing: $12 per billed developer per month for MergeQueue, $12 for FlexReview and $8 for Releases. Each product counts its own developers, so one developer can appear on more than one line.

The **Products** page under Billing shows the usage and subtotal for each product. You can move to Team or Scale from the Billing page at any time. The switch replaces your per-product charges with the single bundle price, and the prorated difference for the current period is charged right away.

</details>

## Free trial

<details>

<summary>Do I need a credit card to sign up?</summary>

No. Every new workspace starts on a 14-day Team trial with no payment method required, and nothing is ever charged during the trial. You add a card only when you choose a paid plan.

</details>

<details>

<summary>What's included in the trial?</summary>

The trial gives you everything in the Team plan. There is no limit on the number of developers during the trial.

</details>

<details>

<summary>What happens when my trial ends?</summary>

If you have not chosen a plan after 14 days, your workspace moves to the Free plan automatically. MergeQueue, FlexReview and Releases are paused; the dashboard, review Inbox and Verify stay available.

Nothing is deleted. Your repositories, queue configuration, ownership rules and history are kept, so choosing Team or Scale later picks up exactly where you left off.

The Billing page shows how many days are left in your trial.

</details>

<details>

<summary>Can I choose a plan during the trial? When am I charged?</summary>

Yes. Choose Team or Scale from the Billing page and add a card. Your workspace moves to the new plan immediately, but you keep your free days: the first charge happens on the day the trial would have ended, at the price of the plan you picked. If no developer has been billed by then, billing starts with your first billed developer.

</details>

<details>

<summary>Can I extend or restart my trial?</summary>

Each workspace gets one trial, and a workspace that has moved to Free cannot re-enter it from the Billing page. If you need more time to evaluate Aviator, contact [support@aviator.co](mailto:support@aviator.co).

</details>

## Billed developers

<details>

<summary>When does a developer stop being billed?</summary>

Aviator reviews billed developers once a week. A developer is released from billing when either of these is true:

* They are no longer a collaborator on any of your repositories that have MergeQueue or FlexReview enabled.
* They have not created a pull request in the last 90 days.

This happens automatically. If someone has left your team, removing them from the repositories on GitHub releases them at the next weekly review. If they become active again later, they are billed again from that point. There is no manual toggle to remove a developer; if you believe someone is billed in error, contact [support@aviator.co](mailto:support@aviator.co).

</details>

<details>

<summary>When do new or removed developers show up on my invoice?</summary>

On a **monthly** plan, changes settle at your next renewal. You are charged for the developers who are billed at the moment the renewal runs. A developer who becomes billed mid-month appears on the next invoice, and one released before the renewal is not on it. There are no mid-cycle proration lines.

Because developers are released by the weekly review, someone you remove from GitHub a day or two before your renewal may still be on that invoice.

On an **annual** plan, developers added mid-year are prorated for the remainder of the year.

</details>

## Changing, cancelling and resuming your plan

<details>

<summary>Who can change the plan or billing details?</summary>

Only workspace **admins** can choose or switch a plan, add or change the payment method, cancel or resume the subscription, download invoices and edit billing emails. Maintainers and members can see their own Verify usage but cannot change billing.

</details>

<details>

<summary>How do I upgrade from Team to Scale?</summary>

Go to Billing, click **Change plan** and pick Scale. The upgrade applies immediately. The prorated difference for the rest of your current billing period is charged today, and your billing date does not change.

Your Verify allowance switches from the shared pool to the per-developer allowance, starting a fresh cycle at the time of the switch.

</details>

<details>

<summary>How do I downgrade from Scale to Team?</summary>

Go to Billing, click **Change plan** and pick Team. You keep Scale until your next renewal. That renewal is charged at the Team price, and Team starts then. The Billing page shows the scheduled date.

You can undo a scheduled downgrade any time before it takes effect with the **Keep Scale** button.

When the downgrade takes effect, ChangeSets are no longer available and Verify moves back to the shared pool.

</details>

<details>

<summary>How do I cancel my plan?</summary>

Admins can cancel from the Billing page with **Cancel subscription**. You keep full access until the end of the period you have already paid for. After that your workspace moves to the Free plan and no further charges are made. If you have not been charged yet, for example because no developer has been billed, the workspace moves to Free right away.

You can change your mind at any point before the period ends with **Resume subscription**. While a cancellation is pending you cannot switch plans; resume first, then switch.

Partial periods are not refunded. If you prefer, you can also email [support@aviator.co](mailto:support@aviator.co) to cancel.

</details>

<details>

<summary>What happens to my data and configuration on the Free plan?</summary>

Nothing is deleted. Your connected repositories, queue configuration, FlexReview rules, release settings and history are all kept. MergeQueue, FlexReview and Releases stop running until you choose a paid plan again, at which point everything resumes with the same configuration.

</details>

## Payments, invoices and billing contacts

<details>

<summary>How do I add or update my credit card?</summary>

On a trial or the Free plan, you add your first card while choosing a plan. Click **Choose a plan**, pick Team or Scale, then **Continue to payment** and enter your card. There is no separate option to add a card before picking a plan.

Once you are on a paid plan, you can replace the card any time with **Change payment method** under **Billing method**.

Card details are entered in a secure Stripe form and stored by Stripe; Aviator never sees your full card number.

</details>

<details>

<summary>When am I charged?</summary>

Your subscription renews on the date shown as **Next payment on** in Billing, monthly or yearly depending on your cycle. The first charge happens when your trial ends, or, if you chose a plan from the Free plan, as soon as your first developer is billed.

</details>

<details>

<summary>What happens if a payment fails?</summary>

Stripe retries the payment automatically over the following days. To fix it, update your card under **Billing method** on the Billing page; the next retry will use the new card.

If every retry fails, the subscription ends and your workspace moves to the Free plan. Your configuration is kept, and choosing a plan again restores access.

</details>

<details>

<summary>Where can I find my invoices?</summary>

The **Payment history** section on the Billing page lists every charge. Use the download icon next to a charge to get its invoice.

</details>

<details>

<summary>Can I change who receives billing emails?</summary>

Yes. Add one or more addresses under **Billing emails** on the Billing page. Billing notices, including the notice when your workspace runs out of Verify runs, are sent to these addresses.

</details>

## Verify runs

<details>

<summary>How many Verify runs do I get?</summary>

| Plan | Verify runs |
| --- | --- |
| Free and Team trial | 10 runs a month, shared by the workspace |
| Team | 10 runs a month, shared by the workspace |
| Scale | 50 runs a month for each billed developer |
| Enterprise with your own AI key | Unlimited |

Runs renew once a month on your workspace's cycle date, which is anchored to when the workspace was created rather than to the calendar month or your invoice date. Your reset date is shown on **Billing → Verify**. Unused runs do not roll over. Changing plans starts a fresh cycle on the new allowance.

</details>

<details>

<summary>What counts as a Verify run?</summary>

One run is used each time Verify starts verifying a pull request, whether it was triggered automatically by a push or manually by a developer. A run cancelled after it started still counts.

The following do **not** use a run:

* A run that never started, for example because a newer push replaced it while it was waiting.
* A run that found nothing to verify.
* A run that failed because of an error on Aviator's side.

</details>

<details>

<summary>Which developer does a run count against on Scale?</summary>

On Scale each billed developer has their own allowance. A run counts against the person who triggered it manually; an automatic run counts against the author of the pull request.

The author must have their GitHub account connected to Aviator so the run can be attributed. If it is not, the run is not started and Verify asks them to connect GitHub under **Settings → Personal → Integrations**. Bots are never charged a run.

</details>

<details>

<summary>What happens when I run out of Verify runs?</summary>

New runs are blocked rather than queued. Automatic verification is skipped and Verify posts a note on the pull request explaining why, once per pull request per cycle. Your billing contacts also receive a notice the first time the allowance is used up in a cycle.

On Team the whole workspace shares one pool, so everyone is paused until the cycle renews. On Scale only the developer who used up their allowance is paused; the rest of the team is unaffected.

Verify chat is also paused while you are out of runs. To continue right away, upgrade your plan from the Billing page.

</details>

<details>

<summary>Where can I see my Verify usage?</summary>

* **Billing → Verify** shows runs remaining, granted and used for the cycle, with a per-developer table on Scale.
* The **runs left** pill on the Verify dashboard shows your own remaining runs and when they reset.

</details>

<details>

<summary>Can I buy extra Verify runs?</summary>

Extra runs are not sold individually from the Billing page today. To get more runs, upgrade from Team to Scale for the per-developer allowance, or talk to [sales@aviator.co](mailto:sales@aviator.co) about an Enterprise plan.

</details>

<details>

<summary>Can I use my own Anthropic API key for Verify?</summary>

Bring-your-own-key for Verify is available on Enterprise plans. Verify then runs on your key with no run limit, and your usage page shows recent run counts instead of a balance. Contact [sales@aviator.co](mailto:sales@aviator.co) to enable it.

</details>

## Runbooks

<details>

<summary>How are Runbooks billed?</summary>

Runbooks run on your own LLM API key. Aviator does not charge per Runbook or per run; the model usage is billed to you directly by your LLM provider. Add your key on the [Anthropic Key](https://app.aviator.co/verify/settings/anthropic-key) settings page before starting a Runbook. Without a key, Runbooks will not start and Aviator shows a message asking you to add one.

If Runbooks is not enabled on your workspace, contact [support@aviator.co](mailto:support@aviator.co).

</details>

## Enterprise and self-hosted

<details>

<summary>How does billing work on an Enterprise plan?</summary>

Enterprise workspaces are billed under their contract rather than by card, and plan changes go through your Aviator contact. The Billing page shows your plan and billed developers, but the self-serve plan switch is disabled; it asks you to contact support instead.

To discuss Enterprise pricing, contact [sales@aviator.co](mailto:sales@aviator.co).

</details>

<details>

<summary>Is self-hosted (on-premise) Aviator billed differently?</summary>

Yes. Self-hosted installations are licensed under a contract and invoiced directly. There is no card or Stripe subscription involved. See the [On-Premise Installation](../on-premise-installation/README.md) guide for setup details.

</details>

Still have a question? Email [support@aviator.co](mailto:support@aviator.co).
