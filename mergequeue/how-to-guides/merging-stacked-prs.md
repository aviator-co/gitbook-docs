---
description: >-
  Use our how-to guide for instructions on merging stacked PRs. Learn about
  queueing actions, core merge behaviors for stacked PRs, and separating merge
  commits.
---

# Merging stacked PRs

Aviator offers an [<mark style="color:blue;">open-source CLI</mark>](https://github.com/aviator-co/av) for managing stacked PRs within GitHub. When used alongside Aviator MergeQueue, it simplifies the process of validating and merging those stacked PRs (or subset of those stacked PRs) together.

When requested to merge stacked PRs, Aviator will validate all the stacked PRs as if they were a single PR, and then merge them together after validation. The merge behavior may be slightly different depending on the [<mark style="color:blue;">queue mode</mark>](../concepts/queue-modes.md).

## Queueing action

To merge a subset of the stack, request a merge for the topmost PR in that subset. So for instance, if your stack is:

```
master <- PR#1 <- PR#2 <- PR#3 <- PR#4
```

Requesting a stack-merge action for `PR#3` will validate `PR#1`, `PR#2` and `PR#3` leaving `PR#4` untouched. After merging, the stack would look like:

```
master <- PR#4
```

There are a few ways to request queueing a stack of PRs.

* **Chrome Extension (recommended)**: This is the simplest way to request way since the user experience for this is very similar to merging a regular PR. Go to `PR#3` from above example in GitHub and click "Queue pull request" button:

<figure><img src="../../.gitbook/assets/Screenshot 2024-06-21 at 12.25.14 PM.png" alt=""><figcaption><p>Queue pull request action for stacked PRs</p></figcaption></figure>

* **CLI**: Another way to queue the PR is via the `av` command line. Simply checkout the branch associated with `PR#3` and run the command:

```
av pr queue
```

Note that, using this command requires [<mark style="color:blue;">authenticating the CLI with your Aviator account</mark>](https://docs.aviator.co/aviator-cli#setting-up-aviator-mergequeue).

* **Slash command**: A stack or sub-stack can also be merged by commenting a [slash command](../../flexreview/reference/flexreview-slash-commands.md) from the GitHub interface:

```
/aviator stack merge
```

{% hint style="info" %}
Note that the slash command `/aviator merge` does not work to merge stacked PRs.
{% endhint %}

## Merge behavior

Depending on the queue mode being used, the merge behavior for stacked PRs can vary.&#x20;

### Sequential mode (default mode)

Taking the example above, in sequential mode:

* Aviator will consider `PR#1`, `PR#2` and `PR#3` as a single PR in the queue.
* When this PR reaches the top of the queue, Aviator only updates `PR#3` with the target branch (mainline), and run the CI.
* Once the CI passes, Aviator will change the base branch of `PR#3` and merge it
* &#x20;Since `PR#3` is stacked on top of `PR#1` and `PR#2`, merging PR#3 also merges all the changes associated with `PR#1` and `PR#2`.
* Since GitHub does not recognize that `PR#1` and `PR#2` are already merged, Aviator automatically closes them and applies the GitHub label `merged-by-mq` to represent that the PRs are indeed merged.

See also [<mark style="color:blue;">Separating the merge commits</mark>](merging-stacked-prs.md#separating-the-merge-commits).

### Parallel mode

Similar to Sequential mode, parallel mode also considers the stack as a single PR in the queue. In parallel mode:

* Aviator creates a batch using the head branch of `PR#3` including all the changes of `PR#1` and `PR#2` as well.
* After the batch passes CI, Aviator changes the base branch of `PR#3` and merges it.
* Since `PR#3` is stacked on top of `PR#1` and `PR#2`, merging PR#3 also merges all the changes associated with `PR#1` and `PR#2`.
* As in Sequential mode, Aviator closes `PR#1` and `PR#2`, while applying the GitHub label `merged-by-mq` to represent that the PRs are indeed merged.

See also [<mark style="color:blue;">Separating the merge commits</mark>](merging-stacked-prs.md#separating-the-merge-commits)<mark style="color:blue;">.</mark>

### Fast forwarding

[<mark style="color:blue;">Fast forwarding mode</mark>](../concepts/parallel-mode/fast-forwarding.md) provides a  simpler experience for merging stacked PRs. Since in case of fast-forwarding, we fast-forward the mainline to commits in draft PRs, the CI is already passing. This allows Aviator to merge the PRs individually without GitHub blocking the commits.

In fast-forwarding mode:

* When the stack with `PR#3` is requested merging, Aviator creates one squash commit for each PR in the stack.
* After CI passes, the mainline is fast-forwarded to the latest commit in the batch associated with `PR#3`, this includes separate commits associated with `PR#2` and `PR#1` in the linear history.

## Separating the merge commits

By default the Sequential and Parallel modes create a single commit in the mainline for all of the stacked PRs that are queued together. This is because GitHub does not let an app account bypass the branch protection rules. Even when allowed to bypass the rules, GitHub will let the Aviator app only bypass the approval requirement and PR creation requirement, not the CI validation requirement.

### Rulesets

This capability of bypassing the CI requirement is now available using GitHub’s [Rulesets](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/managing-rulesets-for-a-repository). Rulesets offer feature parity with the configurations you use in classic branch protection rules and you should be able to migrate all your existing configurations to using Rulesets.

Once migrated to Ruleset, you can add Aviator app in the bypass list.

<figure><img src="../../.gitbook/assets/rulesets.avif" alt=""><figcaption><p>Add Aviator GitHub app to Bypass list</p></figcaption></figure>

### **Configuring separate commits**

Once Rulesets are configured and other GitHub branch protection rules are disabled, you can configure Aviator to start creating separate commits for stacked PRs. To do so, set the following in the `merge_strategy` section of the configuration YAML.

```
merge_strategy:
  use_separate_commits_for_stack: true
```

For details, refer to the [<mark style="color:blue;">configuration reference</mark>](https://docs.aviator.co/mergequeue/reference/complete-reference-guide#merge-strategy).

If you need assistance in setting up the Rulesets, please contact [howto@aviator.co](mailto:howto@aviator.co).
