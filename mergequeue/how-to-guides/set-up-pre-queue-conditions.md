---
description: >-
  This guide provides steps to configure pre-queue conditions. Get detailed
  instructions with screenshots and samples in Aviator Documentation.
---

# Set up Pre-Queue Conditions

This guide describes how to setup [<mark style="color:blue;">pre-queue conditions</mark>](../concepts/pre-queue-conditions.md).

## RegExp PR Title and Body Validation

Aviator supports validating the PR title and description. For instance, if you want to require each PR title to have a ticket number, or if you have a checklist of items in the body, you can setup the preconditions:

```yaml
preconditions:
  validations:
   - name: Require JIRA ticket in title
     match:
       type: title
       regex:
       - '^\[AVT\-\d+\].+$'
   - name: Require tests have been run checkbox
      match:
        type: body
        regex:
        - '^\[x\] All relevant tests have been run$'
    - name: Require documentation checkbox
      match:
        type: body
        regex:
        - '^\[x\] The documentation has been updated$'
```

Using this now, Aviator will look for a PR title starting with “\[AVT-”, and would require the body to have the following two checks marked as done:

```
[x] All relevant tests have been run
[x] The documentation has been updated
```

<figure><img src="../../.gitbook/assets/Screen Shot 2023-04-19 at 10.42.00 AM.png" alt=""><figcaption></figcaption></figure>

When the checks fail, you will see the “name” of the validation reported in the comment as failure, blocking the PR from getting queued:

<figure><img src="../../.gitbook/assets/Screen Shot 2023-04-19 at 10.44.00 AM.png" alt=""><figcaption></figcaption></figure>
