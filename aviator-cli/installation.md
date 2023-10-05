# Installation

The Aviator command line tool (invoked as `av`) can be used to streamline and automate common tasks within your Git and GitHub workflows. Currently, the tool is used primarily to manage Stacked PRs<mark style="color:blue;">.</mark>

## First-time setup

### 1. Download the command line tool

The `av` tool can be installed in a few ways, depending on your operating system and package manager preferences.

#### Homebrew (MacOS, Linux)

First, if not already done, [<mark style="color:blue;">install Homebrew</mark>](https://brew.sh/)<mark style="color:blue;">.</mark>

Then, install `av` using the Homebrew tap.

```
brew install aviator-co/tap/av
```

#### Download executable (advanced, all operating systems)

Download the latest `av` executable from the [<mark style="color:blue;">GitHub releases page on the</mark> <mark style="color:blue;">`av`</mark> <mark style="color:blue;">repository</mark>](https://github.com/aviator-co/av/releases). Extract the archive and add the executable to your `PATH`.

### 2. Connect av to GitHub

The `av` tool uses a [GitHub personal access token (PAT)](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token#creating-a-personal-access-token-classic) to authenticate with GitHub on your behalf. This is required to create and inspect PRs (e.g., when using the `av pr create` command). The generated token should have the `repo` scope (all other scopes can be left un-checked).

![Required permissions for the GitHub personal access token that you will use with av](<../.gitbook/assets/Screen Shot 2022-05-26 at 11.20.35 AM.png>)

Add the GitHub token to your `av` config file (make sure to create the `~/.av` directory first):

{% code title="~/.av/config.yaml" %}
```yaml
github:
    # Replace this value with the token you created in the step above
    token: "ghp_abcdefghijklmnop"
```
{% endcode %}

### 3. Initialize your repository

Finally, in the repo where you want to use `av`, make sure to initialize the repository. From within your git repository, run the following command:

```
av init
```


## Upgrade <a href="#upgrade" id="upgrade"></a>

#### Homebrew

Run the following command to upgrade `av` if it was installed with Homebrew.

```
brew upgrade av
```

#### Download executable

Follow the [<mark style="color:blue;">installation instructions above</mark>](installation.md#download-executable-advanced-all-operating-systems) and overwrite the previous `av` binary with the newly downloaded binary.
