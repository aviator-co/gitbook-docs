---
description: FAQs and troubleshooting for Aviator StackedPRs CLI, av cli.
---

# FAQs and Troubleshooting

{% hint style="info" %}
If you have a question or issue that's not answered below, feel free to reach out to us at [<mark style="color:blue;">support@aviator.co</mark>](mailto:support@aviator.co).
{% endhint %}

### Aviator reports an pending status check with state "unknown"

This usually indicates that MergeQueue is configured to require a particular status check before merging a PR, but your pull request hasn't executed that check because your repository is configured to only run checks for pull requests that target your repository default branch (e.g., `main` or `master`). This happens because PRs in a stack (other than the first) do not explicitly target the default branch.

If using GitHub actions, make sure your workflow configuration (`.github/workflows/<name>.yml`) is set to run the check on all branches.

```yaml
# Sample configuration, adapt accordingly
on:
  pull_request:
    branches:
    - "*"
```

### Does each PR in the stack need to pass CI?

Yes. Aviator will refuse to merge a stack that has a failing PR within it.

One common strategy to make incremental changes easier is the use of feature flags. This allows your work to be incorporated into your codebase even before it’s ready for public consumption, reducing the chance for merge conflicts and keeping your feature branches up to date. For example, your first PR might look something like this:

```python
# Only set to True during development
ENABLE_AUTOMATIC_CAT_GIFS = False

def greet_user(user):
    ...
    if ENABLE_AUTOMATIC_CAT_GIFS:
        send_sms(user.phone_number, "<gif of an adorable cat>")
```

### What happens if I use the GitHub merge button instead of Aviator to merge a stacked PR?

Using the GitHub merge button merges the PR where the button is clicked into its base branch. In the case of stacked PRs, this means you might be merging PR #2 into PR #1, which is generally not what's desired.

### Why not just use feature branches?

Feature branches involve dedicating a branch for a given feature. Instead of making PRs to the base branch of the repository, PRs are made into the feature branch. When the feature is complete, the feature branch can then be merged into the base branch.

While this works great for some teams and projects, feature branches (especially those that are relatively long lived) run the risk of drifting out of sync with the main branch. When it comes time to merge the feature branch into the main branch, there’s a much greater chance for conflicts — either explicit merge conflicts or more subtle semantic conflicts (e.g., unexpected interactions between two components). Many teams and organizations now use a method known as [<mark style="color:blue;">trunk based development</mark>](https://www.atlassian.com/continuous-delivery/continuous-integration/trunk-based-development) which helps solve some of these issues.

Stacked PRs enables engineers to be more productive, especially when using trunk based development. While it’s possible for stacks to drift out of date with the main branch, this is less of an issue since stacks are usually smaller in size and shorter lived than feature branches, and Aviator helps you keep your stacks up to date with the main branch.
