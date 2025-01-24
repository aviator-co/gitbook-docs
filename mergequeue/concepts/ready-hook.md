---
description: >-
  Learn what are ready hooks in MergeQueue and how they work. User-defined ready
  hooks can execute custom JavaScript code to control MergeQueue behavior.
---

# Ready Hook

MergeQueue supports user-defined _ready hooks_ which can execute custom JavaScript code to finely control how MergeQueue behaves.

The ready hook is executed once whenever a pull request is marked as ready-to-merge (usually via adding the repository's configured label, by using a [slash command](../reference/slash-commands.md), or using the [Aviator API](../../api/)).

The ready hook is defined by creating a file in your GitHub repository at `.aviator/mergequeue/ready.js` and should define a function `ready` that will be called by the [Pilot JavaScript runtime](../../pilot-automated-actions/js-execution.md).

{% hint style="info" %}
See how you can use [<mark style="color:blue;">Ready Hook to reduce queue failures due to staleness</mark>](reducing-queue-failures-due-to-staleness.md).
{% endhint %}
