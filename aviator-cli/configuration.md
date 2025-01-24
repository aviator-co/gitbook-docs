---
description: >-
  Read documentation to configure Aviator's StackedPRs CLI, also known as av
  CLI.
---

# Configuration

## Configuration

The `av` CLI looks for a config file in a few places:

* `~/.config/av/config.yaml`
* `~/.av/config.yaml`
* `.git/av/config.yaml` (if running the `av` command inside of a git repository)

### Config option reference

{% code title="config.yaml (sample)" %}
```yaml
# Configuration that controls how av communicates with GitHub 
github:
    # REQUIRED (for `av pr create`)
    # The GitHub personal access token that av uses to authenticate to GitHub
    # You can create a token at https://github.com/settings/tokens.
    token: "ghp_abcdefghijklmnop"

    # The GitHub base URL to use.
    # Only set this if you use an on-prem enterprise GitHub installation
    # (leave blank if your repo is hosted on github.com)
    baseUrl: "https://github.com"

pullRequest:
    # If true, always open PRs as a draft.
    # If false, PRs will be opened as ready-for-review unless the --draft flag
    # is specified on commands that create pull requests.
    draft: false
    
    # If true, pull requests will be temporarily transitioned to draft state
    # while being rebased. This avoids accidentally adding unnecessary reviewers
    # to a pull request due to a CODEOWNERS file while the pull request is in a
    # transient state. This only applies when a pull request's base branch is
    # changing.
    # If not specified in the config, the default value is true if there is a
    # CODEOWNERS file present and false otherwise.
    rebaseWithDraft: false
    
    # If true, open a web browser to the pull request page whenever a pull
    # request is created for the first time.
    openBrowser: true
    
    # By default, when the pull request title contains "WIP", it automatically sets
    # the PR as a draft PR. Setting this to true suppresses this behavior.
    noWipDetection: false
    
    # Branch prefix to use for creating new branches.
    branchNamePrefix: "alice/"
    
    # If true, the CLI will automatically add/update a comment to all PRs linking
    # other PRs in the stack. False by default.
    writeStack: false

aviator:
    # The base URL for the Aviator API to use. The default is https://api.aviator.co.
    # This can be changed if you use on-prem installation.
    apiHost: "https://api.aviator.co"
    # API token to use for authenticating to the Aviator API.
    apiToken: "..."

# Additional trunk branch names. By default, av CLI uses the branch pointed by HEAD.
additionalTrunkBranches:
    - dev

# The remote name. By default av CLI uses "origin".
remote: "origin"
```
{% endcode %}
