---
description: >-
  Learn how to enable fast-forwarding in MergeQueue. Use our guide for detailed
  instructions, sample configuration, and additional optimization settings.
---

# Set Up Fast-Forwarding

## Enable fast-forwarding in MergeQueue

* Set `merge_mode` type to `parallel` in the yaml configuration.
* Set `use_fast_forwarding` to `true` in the `parallel_mode` configuration.
* Update required checks. There are two separate sets of required checks you can configure. By default, Aviator validates the checks that are set as required on GitHub. You can override those for the original PRs in the `required_checks` section (under `preconditions`), and you can set separate checks for the parallel pipeline's draft PRs in the `override_required_checks` section (under `parallel_mode`).

## Sample configuration

```yaml
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

## Grant Aviator access to the protected branch

### What Aviator needs, and why

Fast-forwarding does not merge a pull request into your base branch. Aviator has already built and validated the exact commit you want on a temporary branch, so all that is left is to move the base branch reference forward onto that commit — no merge commit, no PR merge event.

Because there is no pull request merge, your branch protection sees a direct update to the branch and blocks it. So `aviator-app` needs to bypass two protections on any branch it fast-forwards:

1. **The pull request requirement** — the commit is not arriving via a pull request merge.
2. **The required status check requirement** — the checks ran on Aviator's temporary branch, not on the base branch's own head.

Neither of these weakens the protections for your developers. They apply only to the `aviator-app` actor, and only for a commit whose CI Aviator has already validated.

How you grant this depends on whether the branch is governed by a **ruleset** or a classic **branch protection rule**.

### If you use rulesets

Add `aviator-app` to the ruleset's **Bypass list**. That is the entire setup — a bypass entry covers both the pull request requirement and the required status checks.

### If you use classic branch protection rules

Classic branch protection has no single bypass list, so the same two capabilities are granted through separate settings.

* Add `aviator-app` to `Allow specific actors to bypass required pull requests`:

<figure><img src="../../.gitbook/assets/Screen Shot 2022-10-13 at 3.30.34 PM.png" alt=""><figcaption></figcaption></figure>

* Add `aviator-app` under **Allow force pushes → Specify who can force push**:

![](<../../.gitbook/assets/Screen Shot 2022-07-18 at 9.55.56 AM.png>)

* Add `aviator-app` to `Restrict who can push to matching branches`, only if you use that setting.

<figure><img src="../../.gitbook/assets/Screen Shot 2022-10-13 at 3.45.53 PM.png" alt=""><figcaption></figcaption></figure>

### Why the force-push setting is on that list

This is the setting that most often raises questions, because Aviator does not force push and does not rewrite history. It appears in the list because classic branch protection exposes no other per-actor grant that lets an app update a protected branch reference outside of a pull request merge — the setting is labeled for force pushes, but it is the control that governs direct writes to the reference.

Aviator never rewrites history on your base branch. Once a commit lands on `main` or `master`, Aviator will not revoke, replace, or roll it back — the branch only ever moves forward. Granting this setting does not change that:

* **Aviator never asks for a forced update.** The API call that moves the branch reference omits the `force` flag. GitHub [defaults `force` to `false`](https://docs.github.com/en/rest/git/refs#update-a-reference) and rejects any update that is not a fast-forward. That guarantee is enforced by GitHub, not by Aviator choosing to behave.
* **A rejected update is never retried with force.** If your base branch has moved on and the update is no longer a fast-forward, GitHub returns `Update is not a fast forward`. Aviator resets the queue and re-validates the affected pull requests against the new base commit.
* **The grant is scoped to `aviator-app`.** Choosing *Specify who can force push* rather than *Everyone* means force-push access for your developers is unchanged.
* **The target commit has already passed CI.** Aviator only advances the branch to the head of a temporary branch whose required checks completed successfully, and that commit is always a descendant of the current branch head.

The net effect on your branch is a linear, fast-forward-only advance — the commit that lands is the commit that was tested, and it stays landed.

### Confirming your setup is correct

If any of these permissions are missing, the fast-forward fails and Aviator posts a comment on the affected pull request:

> The Aviator app does not have enough permissions to fast-forward this branch.

That comment is the definitive signal to revisit this section. Merges that succeed confirm the permissions are in place.

## Optimize CI execution rules (optional)

* When creating fast-forward branches, Aviator uses temporary branches. If your CI is configured to run on every commit SHA, you can exclude certain branches from running CI. You can add a criterion to exclude branches with the prefix `mq-tmp-`
* In addition, since the CI has already passed before the default branch gets fast forwarded, you can also exclude your default branch (typically `master` or `main`) for CI execution.

## Conclusion

That’s it, you should be up and running at this point. Give it a go and reach out to us if you have any questions or feedback.

## Learn more

* [<mark style="color:blue;">Fast-Forwarding conceptual overview</mark>](../concepts/parallel-mode/fast-forwarding.md)
