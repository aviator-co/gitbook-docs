# Fast Forwarding

## Overview

Fast-forwarding is a way to linearly move the branch reference from the current head commit to the next commit without creating any additional merge commits. With Aviator’s new fast forwarding feature, we build parallel speculative pipelines in sequential order to achieve this behavior.

## Why Fast Forward?

By using this mode, the history in your Git repository remains linear and CI validation on those speculative commits are verified before the default branch is fast forwarded. This guarantees that the default branch is always green.

## How it works

Fast forwarding works by creating temporary branches in a sequential order for every new PR that is ready to merge. Aviator identifies PRs that are ready through GitHub labels.

![](<../.gitbook/assets/ezgif.com-gif-maker (1).gif>)

When a PR (`PR #1`) is labeled as ready for merging, Aviator picks the head commit for your base branch (typically `master` or `main`), creates a new branch (`branch A`), and applies all the commits of the labeled PR as a squash commit in `branch A`. Aviator will then validate the CI for this new squash commit. If a second PR (`PR #2`) is labeled while the CI for the first one is still running, Aviator will now pick the latest commit from `branch A` and create a new branch (`branch B`) and apply the commits of the second PR as a squash commit to `branch B`. This way the CI validation can happen in parallel using a speculative commit strategy.

Once the CI for PR #1 passes, Aviator automatically fast forwards the head of your base branch to the head of `branch A`. Likewise if the CI for PR #2 passes, it fast forwards the head of your base branch to the head of `branch B`. By using this strategy, Aviator can both maintain the linear history of your PRs and also ensure that the builds pass on each commit.

If the CI for PR #1 fails, Aviator will not discard `branch A`, and will recreate `branch B` with only the commits of `PR #2`. This way Aviator detects failures before the commits hit the base branch, ensuring that the base branch will always remain green.

![](<../.gitbook/assets/ezgif.com-gif-maker (2).gif>)

## Setup

Fast-forwarding can only be enabled with parallel mode, and similar to parallel mode, we create separate PullRequests to represent each PR in the pipeline.

### Step 1: Set the configurations in Aviator

#### Using yaml config

* Set `merge_mode` type to `parallel` in the yaml configuration.
* Set `use_fast_forwarding` to `true` in the `parallel_mode` configuration.
* Update required checks. There are two separate checks that can be configured. Aviator will validate the checks that are set as required on GitHub by default. You can override those rules in the `required_checks` section. You can also override separate required checks for the parallel pipeline.

```yaml
version: 1.0.0
merge_rules:
  labels:
    trigger: "label_name"
preconditions:
    required_checks:
      - check 1
  merge_mode:
    type: "parallel"
    parallel_mode:
      use_fast_forwarding: false
      override_required_checks:
        - check 1
        - check 2
```



### Step 2: Modify your GitHub protected branch settings

* To ensure that Aviator can forward your default branch, it may require additional privileges. Aviator requires permission to be able to force push to the default branch. To do so, you should authorize the Aviator app to be able to force push to the protected branch. We don’t force push the commits, but this is required to be able to fast forward protected branches that requires PullRequests.

![](<../.gitbook/assets/Screen Shot 2022-07-18 at 9.55.56 AM (1).png>)

* If you have CodeOwners review requirements in your branch protection rules, you should also add `aviator-app` to `Allow specific actors to bypass required pull requests`:

<figure><img src="../.gitbook/assets/Screen Shot 2022-10-13 at 3.30.34 PM.png" alt=""><figcaption></figcaption></figure>

* In addition, add `aviator-app` bot in `Restrict who can push to matching branches` only if you use this setting.

<figure><img src="../.gitbook/assets/Screen Shot 2022-10-13 at 3.45.53 PM.png" alt=""><figcaption></figcaption></figure>

### Step 3 (Optional): Optimize the CI execution rules

* When creating fast forward branches, Aviator uses temporary branches. If your CI is configured to run on every commit SHA, you can exclude certain branches from running CI. You can add a criterion to exclude branches with the prefix `mq-tmp-`
* In addition, since the CI has already passed before the default branch gets fast forwarded, you can also exclude your default branch (typically `master` or `main`) for CI execution.

That’s it, you should be up and running at this point. Give it a go and reach out to us if you have any questions or feedback.
