---
description: >-
  Get instructions on customizing sticky comments in Aviator with a
  configuration example. Sticky comments can provide you with the status of a PR
  within GitHub.
---

# Customize Sticky Comments

Aviator uses [<mark style="color:blue;">sticky comments</mark>](../concepts/sticky-comments.md) to give you the status of a PR within GitHub.

You can customize the sticky comment to show up when the PR is created, when it’s ready for review, or when it’s queued. You can also add your custom messages that could be helpful to link your own FAQs or self help documents for your team.

Here’s a sample config, see `merge_rules.status_comment` in [<mark style="color:blue;">the schema documentation</mark>](https://app.aviator.co/schema/index.html#aviator_config_yaml.json).

```
merge_rules:
  status_comment:
    # Optional. Valid values are "always", "queued", or "never".
    # Default value is "always".
    publish: "always"
    # A message to include when the pull request is in the open state.
    open_message: "..."
    # A message to include when the pull request is in the queued state.
    queued_message: "..."
    # A message to include when the pull request is in the blocked state.
    blocked_message: "..."

```

Comments are enabled by default in your repository, you can modify this configuration from the config file:

```
merge_rules:
  enable_comments: true
```
