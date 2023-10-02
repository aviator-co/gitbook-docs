# Git Subcommand Aliasing

You can map Aviator CLI command as `git` subcommand. In your `.gitconfig`, you can add aliases:

{% code title="~/.gitconfig" %}
```ini
# Do not forget an exclamation point before av.
[alias]
    sync = !av stack sync
    tree = !av stack tree
    pr = !av pr
```
{% endcode %}

With the config above, `git sync` will execute `av stack sync`.

This utilizes a feature that `git` provides. You can see the details in [git-config(1)](https://git-scm.com/docs/git-config#Documentation/git-config.txt-alias).
