---
description: How to guide for Git Subcommand Aliasing when using StackedPRs CLI.
---

# Git Subcommand Aliasing

You can map Aviator CLI command as `git` subcommand. In your `.gitconfig`, you can add aliases:

{% code title="~/.gitconfig" %}
```ini
# Do not forget an exclamation point before av.
[alias]
    sync = !av sync
    tree = !av tree
    pr = !av pr
```
{% endcode %}

With the config above, `git sync` will execute `av sync`.

This utilizes a feature that `git` provides. You can see the details in [<mark style="color:blue;">git-config(1)</mark>](https://git-scm.com/docs/git-config#Documentation/git-config.txt-alias).
