---
description: >-
  Learn how to enable fast-forwarding in MergeQueue. Use our guide for detailed
  instructions, sample configuration, and additional optimization settings.
---

# Set Up Fast-Forwarding

## Enable fast-forwarding in MergeQueue

* Set `merge_mode` type to `parallel` in the yaml configuration.
* Set `use_fast_forwarding` to `true` in the `parallel_mode` configuration.
* Update required checks. There are two separate checks that can be configured. Aviator will validate the checks that are set as required on GitHub by default. You can override those rules in the `required_checks` section. You can also override separate required checks for the parallel pipeline.

## Sample configuration

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
      use_fast_forwarding: true
      override_required_checks:
        - check 1
        - check 2
```

## Allow Aviator to update your protected branch

Fast-forwarding moves the branch reference directly to the commit that CI already validated, instead of merging through a pull request. GitHub treats that as a direct update to the branch, so when your target branch is protected, Aviator has to be allowed to make it.

### Do I need to change these settings?

You need them if the target branch is covered by branch protection rules or a repository ruleset that requires pull requests or restricts who can push. If the target branch has no such rules, fast-forwarding works without any extra permission.

If you are not sure which applies, enable fast-forwarding and watch the first merge. When a rule blocks the update, GitHub rejects it and Aviator reports the rejection on the pull request, naming the rule that was violated.

### Is this safe?

Aviator sends the branch update **without the force flag**. GitHub therefore applies it only when the update is a genuine fast-forward, meaning the new commit already has the current branch head as an ancestor. If that is not true, GitHub rejects the request. This operation cannot rewrite, reorder, or drop commits that are already on your branch.

The permission is still required because GitHub evaluates branch protection on *any* direct update to a branch reference, regardless of whether that update is a fast-forward. Several of the settings that grant it are worded in terms of force pushes, which is why the setup below asks for a permission broader than what Aviator actually performs.

### Branch protection rules

* Authorize the Aviator app to update the protected branch. In GitHub's classic branch protection settings this permission is presented as force-push access, so `aviator-app` needs to be listed there even though Aviator does not force push.

![](<../../.gitbook/assets/Screen Shot 2022-07-18 at 9.55.56 AM.png>)

* If you have CODEOWNERS review requirements in your branch protection rules, you should also add `aviator-app` to `Allow specific actors to bypass required pull requests`:

<figure><img src="../../.gitbook/assets/Screen Shot 2022-10-13 at 3.30.34 PM.png" alt=""><figcaption></figcaption></figure>

* In addition, add `aviator-app` bot in `Restrict who can push to matching branches` only if you use this setting.

<figure><img src="../../.gitbook/assets/Screen Shot 2022-10-13 at 3.45.53 PM.png" alt=""><figcaption></figcaption></figure>

### Repository rulesets

Rulesets are evaluated separately from classic branch protection, and a repository can have both. If a ruleset targets your default branch, add `aviator-app` to that ruleset's **bypass list**, under **Settings > Rules > Rulesets** in your repository.

The rule that most commonly blocks fast-forwarding is **Require a pull request before merging**, because fast-forwarding updates the branch reference rather than merging a pull request. When it blocks a merge, GitHub returns an error like:

```
Repository rule violations found

Changes must be made through a pull request.
```

If that message also mentions a required status check, the ruleset is still the cause. Adding `aviator-app` to the bypass list resolves both.

## Optimize CI execution rules (optional)

* When creating fast-forward branches, Aviator uses temporary branches. If your CI is configured to run on every commit SHA, you can exclude certain branches from running CI. You can add a criterion to exclude branches with the prefix `mq-tmp-`
* In addition, since the CI has already passed before the default branch gets fast forwarded, you can also exclude your default branch (typically `master` or `main`) for CI execution.

## Conclusion

That’s it, you should be up and running at this point. Give it a go and reach out to us if you have any questions or feedback.

## Learn more

* [<mark style="color:blue;">Fast-Forwarding conceptual overview</mark>](../concepts/parallel-mode/fast-forwarding.md)
