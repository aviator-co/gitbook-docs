---
description: >-
  Guide on creating an access token for interacting with GitHub for
  authentication when using Aviator StackedPRs CLi.
---

# Create an Access Token

## GitHub Personal Access Token

If [GitHub CLI](https://cli.github.com/) is installed and set up on your computer, av CLI automatically uses the same credential for interacting with GitHub. Alternatively, you can create a personal access token (classic) to authenticate with GitHub on your behalf.

1. Navigate to [https://github.com/settings/tokens/new](https://github.com/settings/tokens/new).
2. Create a name and expiration for your token.
   * The generated token should have the `repo` scope (all other scopes can be left un-checked).

![Required permissions for the GitHub personal access token that you will use with av](<../../.gitbook/assets/Screen Shot 2022-05-26 at 11.20.35 AM.png>)

Put the created Personal Access Token in `~/.av/config.yaml`.

<pre class="language-yaml" data-title="~/.av/config.yaml" data-line-numbers><code class="lang-yaml"><strong>github:
</strong>    token: "ghp_abcdefghijklmnop"
</code></pre>

## Aviator User Access Token

User Access Tokens are used to authenticate with the Aviator REST API and Aviator GraphQL API.

1. Navigate to [https://app.aviator.co/settings/personal/api\_token](https://app.aviator.co/settings/personal/api_token).
2. Create a name and expiration for your token.
   * The token should be in the format `av_uat_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`. Add it to your Aviator configuration file `~/.av/config.yaml` as shown below.

{% code title="~/.av/config.yaml" lineNumbers="true" %}
```yaml
aviator:
    apiToken: "av_uat_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
```
{% endcode %}

You can verify that your token works with `av auth`.

```
$ av auth
Logged in to GitHub as <your_email>.
```
