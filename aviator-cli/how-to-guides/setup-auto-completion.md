---
description: Set up shell auto completion when using StackedPRs from Aviator.
---

# Setup Auto Completion

Aviator CLI comes with shell auto completion

## Bash

This depends on `bash-completion` package. You need to install this with your OS package managers. Then, add the following line to the bashrc.

{% code title=".bashrc" %}
```
source <(av completion bash)
```
{% endcode %}

### Note on macOS

You will need to use bash from Homebrew (`brew install bash bash-completion`) as the pre-installed bash version is old. Follow the instruction shown after installation to setup bash-completion.

## Zsh

Setup `compinit`, and add the following line to the zshrc.

{% code title=".zshrc" %}
```
source <(av completion zsh)
```
{% endcode %}
