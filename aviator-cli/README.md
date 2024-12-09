---
icon: square-terminal
---

# Stacked PRs CLI

Aviator is a suite of productivity tools purpose-built to reduce friction in development workflows. `av` is an [<mark style="color:blue;">open-source CLI</mark>](https://github.com/aviator-co/av) built and managed by Aviator to interact with GitHub and Aviator service. The primary use case is creating and managing Stacked PRs.



{% embed url="https://youtu.be/xSopIrkf2KM" %}
Managing stacked PRs using Aviator CLI
{% endembed %}

{% hint style="info" %}
The Stacked PRs CLI [<mark style="color:blue;">does not require creating an Aviator account</mark>](faqs-and-troubleshooting/working-with-aviator.md) and can be [<mark style="color:blue;">installed directly</mark>](installation.md).
{% endhint %}

## Why stacked PRs

Stacked PRs are useful when you have a lot of code changes that are hard to review in a single PR. Stacked PRs let you break down the changes into smaller PRs that are "stacked" on top of each other, keeping code review manageable. This means you can keep working on the next bit of code related to a feature even while waiting for your previous PR(s) to pass CI, be approved, and merged!

For example, if PR1 is `Add the /books/list route to the backend` and PR2 is `Show the books list in the frontend`, we can stack PR2 on top of PR1. This means that you don’t have to wait for PR1 to be reviewed and merged before you can start PR2 (and they can even be reviewed by separate people!).

Learn more about the cultural implication of using Stacked PRs in our [<mark style="color:blue;">blog post</mark>](https://www.aviator.co/blog/stacked-prs-code-changes-as-narrative/).

## Getting started

You don’t need an Aviator account to start using the CLI. You can simply install the CLI using your OS package manager. For instance, on Mac you can use Homebrew:

```
brew install aviator-co/tap/av
```

For installation on other platforms or step by step guide, please checkout [<mark style="color:blue;">installation instructions</mark>](https://docs.aviator.co/aviator-cli/installation).

av CLI needs a GitHub credential for creating and updating PRs. Install [GitHub CLI](https://cli.github.com/), and av CLI will use the same credential for interacting with GitHub. Alternatively, you can use a GitHub Personal Access Token. See [create-a-user-access-token.md](how-to-guides/create-a-user-access-token.md "mention").

Finally, initialize the repository so that CLI can start tracking your active branches.

```
av init
```

## Creating a stack

Creating the first branch in the stack is just the same as creating any normal branch in your repository, except now you use the av CLI. The `av branch` command requires one argument which is the name of the branch to create. This command will create a branch on top of the current branch.

```
# Create a new branch off of your repository default branch
# (usually main or master)
av branch "bookstore-backend"

# Now we can do our development work as normal.
mkdir ./books
echo '...' >> ./books/backend.py
git add ./books
git commit -m "Add /books/list route to backend"
```

To create the PR, also use the `av pr` command to ensure that the correct base branch is set in the PR.

```
av pr
```

For the next branch, we use the `av branch` command again, except this time we're branching from `bookstore-backend` instead of `main` since we want to build off of our previous work. Behind the scenes, the CLI sets some internal data to be able to recognize that `bookstore-frontend` is dependent on `bookstore-backend`.

```
​​av branch "bookstore-frontend"
```

And when it comes time to submit our work as PR, we use the `av pr` command again.

When creating this PR, the CLI again automatically sets the base branch in GitHub as `bookstore-backend` rather than `main` to ensure that GitHub shows the diff between `bookstore-frontend` and `bookstore-backend`. Otherwise, it would show all the changes from `bookstore-backend` in the PR for `bookstore-frontend` which would make code review much harder.

Already have git branches created? Take a look at [adopt-a-branch.md](how-to-guides/adopt-a-branch.md "mention")[<mark style="color:blue;">.</mark>](https://docs.aviator.co/aviator-cli/how-to-guides/adopt-a-branch)

## Updating the stack

Since stacked PRs are designed to make code review easier and more incremental, it's likely that you'll need to change code that you wrote earlier in the stack. The `av sync` command is used to make sure every branch is up-to-date with its stack parent.

To edit a branch that is part of a stack, first we need to check it out.

```
git checkout bookstore-backend
```

Then, we can make edits and commit as usual.

```
echo '...' >> ./books/backend.py
git add ./books
git commit -m "Fix 500 error on malformed input"
```

Finally, we can run `av sync` to propagate the changes to all children branches.

```
av sync
```

## Merging the stack

Merging a stack with Aviator is a little different than merging a non-stacked PR. Although you can merge PRs now manually one at a time, the best way to merge the PRs is to use Aviator MergeQueue.

### Setting up Aviator MergeQueue

Aviator MergeQueue is purpose built as a highly scalable stack-aware merge queue. Follow these steps to connect the CLI with Aviator:

1. [<mark style="color:blue;">Sign up</mark>](https://app.aviator.co/auth/register) for a free account with Aviator
2. Walk through the initial steps, and install the Aviator app on GitHub
3. Navigate to user access token page: [<mark style="color:blue;">https://app.aviator.co/account/apitoken</mark>](https://app.aviator.co/account/apitoken)
4. Generate a token and add it to your configuration file at `~/.av/config.yaml`

{% code title="~/.av/config.yaml" lineNumbers="true" %}
```yaml
aviator:
    apiToken: "av_uat_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
```
{% endcode %}

With that, now to merge a partial or full stack, just queue the top most PR of the stack that you would like to merge:

```
av pr --queue
```

After this, Aviator will automatically validate the changes in the stack, and merge all the changes to the target branch (typically main or master). At any time, you can also review the state of your PR using `av pr status`.

## Man pages and help docs

Aviator also publishes man pages, that you can read for more details on the commands:

```
man av-sync
```

In addition, all commands also provide in-line help:

<pre class="language-bash"><code class="lang-bash"><strong>$ av sync --help
</strong>Synchronize stacked branches to be up-to-date with their parent branches.

By default, this command will sync all branches starting at the root of the
stack and recursively rebasing each branch based on the latest commit from the
parent branch.

If the --all flag is given, this command will sync all branches in the repository.

If the --current flag is given, this command will not recursively sync dependent
branches of the current branch within the stack. This allows you to make changes
to the current branch before syncing the rest of the stack.

If the --rebase-to-trunk flag is given, this command will synchronize changes from the
latest commit to the repository base branch (e.g., main or master) into the
stack. This is useful for rebasing a whole stack on the latest changes from the
base branch.

Usage:
  av sync [flags]

Flags:
      --abort                  abort an in-progress sync
      --all                    synchronize all branches
      --continue               continue an in-progress sync
      --current                only sync changes to the current branch
                               (don't recurse into descendant branches)
  -h, --help                   help for sync
      --prune string[="ask"]   delete branches that have been merged into the parent branch
                               (ask|yes|no) (default "ask")
      --push string            push the rebased branches to the remote repository
                               (ask|yes|no) (default "ask")
      --rebase-to-trunk        rebase the branches to the latest trunk always
      --skip                   skip the current commit and continue an in-progress sync

Global Flags:
      --debug         enable verbose debug logging
  -C, --repo string   directory to use for git repository
</code></pre>

## Advanced guides

This is just a quick preview of things you can do with the Aviator CLI. Check our our [<mark style="color:blue;">how to guides</mark>](https://docs.aviator.co/aviator-cli/how-to-guides) for more specific use cases:

* [<mark style="color:blue;">Split and fold branches</mark>](how-to-guides/split-and-fold-pull-requests.md)
* [<mark style="color:blue;">Adopt a branch that was created without av</mark>](how-to-guides/adopt-a-branch.md)
* [<mark style="color:blue;">Navigating the stack</mark>](how-to-guides/add-commits-in-the-stack.md)
* [<mark style="color:blue;">Renaming a branch</mark>](how-to-guides/rename-a-branch.md)
* [<mark style="color:blue;">Setting up auto-complete</mark>](how-to-guides/setup-auto-completion.md)
