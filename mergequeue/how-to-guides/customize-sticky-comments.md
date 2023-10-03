# Customize Sticky Comments

Aviator uses [sticky comments](../concepts/sticky-comments.md) to give you the status of a PR within GitHub.

You can customize the sticky comment to show up when the PR is created, when it’s ready for review, or when it’s queued. You can also add your custom messages that could be helpful to link your own FAQs or self help documents for your team.&#x20;

Here’s a sample config, see the [full documentation here](../configuration-file/complete-reference-guide.md#status-comment).

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