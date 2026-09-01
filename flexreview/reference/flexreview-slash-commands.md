---
description: >-
  View Slash commands you can use with FlexReview to interact with software
  tools more efficiently. Suggest and Show Status commands explained with use
  cases.
---

# Slash commands

{% hint style="info" %}
Post `/aviator help` in a pull request comment to see the available `/aviator` commands across all Aviator products. See the [<mark style="color:blue;">GitHub slash commands reference</mark>](https://docs.aviator.co/mergequeue/reference/slash-commands) for the full list.
{% endhint %}

### Suggest

The suggest command gets a reviewer suggestion as a Pull Request comment. It has additional debugging information in suggestion, such as how many candidates there were, the expert scores of the candidates, what files are owned by which teams, etc..

```
/aviator flexreview suggest
```

### Assign

The assign command is similar to the suggest command, but it also assigns the suggested reviewer.

```
/aviator flexreview assign
```

### Refresh

The refresh command re-processes the FlexReview teams and approvals for the pull request. This is useful if FlexReview missed an event and the reviewer or approval state on the pull request is out of date.

```
/aviator flexreview refresh
```

### Breakglass

The breakglass command overrides the FlexReview approval requirements on the pull request with a recorded reason. The `--reason` option is required. See [<mark style="color:blue;">breakglass scenarios</mark>](../concepts/validation-in-flexreview.md#breakglass-scenarios) for how a breakglass override affects the approval requirements.

```
/aviator flexreview breakglass --reason="Emergency hotfix for production"
```
