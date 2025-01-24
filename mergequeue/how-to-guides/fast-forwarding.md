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
      use_fast_forwarding: false
      override_required_checks:
        - check 1
        - check 2
```

## Modify your GitHub protected branch settings

* To ensure that Aviator can forward your default branch, it may require additional privileges. Aviator requires permission to be able to force push to the default branch. To do so, you should authorize the Aviator app to be able to force push to the protected branch. We don’t force push the commits, but this is required to be able to fast forward protected branches that requires PullRequests.

![](<../../.gitbook/assets/Screen Shot 2022-07-18 at 9.55.56 AM.png>)

* If you have CODEOWNERS review requirements in your branch protection rules, you should also add `aviator-app` to `Allow specific actors to bypass required pull requests`:

<figure><img src="../../.gitbook/assets/Screen Shot 2022-10-13 at 3.30.34 PM.png" alt=""><figcaption></figcaption></figure>

* In addition, add `aviator-app` bot in `Restrict who can push to matching branches` only if you use this setting.

<figure><img src="../../.gitbook/assets/Screen Shot 2022-10-13 at 3.45.53 PM.png" alt=""><figcaption></figcaption></figure>

## Optimize CI execution rules (optional)

* When creating fast-forward branches, Aviator uses temporary branches. If your CI is configured to run on every commit SHA, you can exclude certain branches from running CI. You can add a criterion to exclude branches with the prefix `mq-tmp-`
* In addition, since the CI has already passed before the default branch gets fast forwarded, you can also exclude your default branch (typically `master` or `main`) for CI execution.

## Conclusion

That’s it, you should be up and running at this point. Give it a go and reach out to us if you have any questions or feedback.

## Learn more

* [<mark style="color:blue;">Fast-Forwarding conceptual overview</mark>](../concepts/parallel-mode/fast-forwarding.md)
