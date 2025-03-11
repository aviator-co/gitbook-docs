---
description: >-
  Validation enhances code reviews by validating the required approvals based on
  the defined ownership and selectively dismissing reviewers based on code
  changes.
---

# Validation in FlexReview

## **How Validation Works**

FlexReview Validation applies a status check on GitHub that represents the validation criteria to merge a PR. If the status check is successful (it turns green) it means that the required amount of approvals have been met, otherwise it may be in a pending state (it’s still waiting for reviews) or a failed state (there have been changes requested). This status check can then be enforced as a required check for merging.

FlexReview improves upon the traditional code review approval process by using custom ownership rules while also allowing for smart dismissal of approvals based on the modifications in a given commit. These validation rules are enforced for every commit in a PR and includes a “break glass” override in case of emergencies.

Validation ensures that:

* Approvals are applied dynamically based on [custom ownership](https://docs.aviator.co/flexreview/concepts/recursive-ownership) definitions.
* Reviewers are dismissed selectively based on diff changes rather than revoking all approvals indiscriminately on each commit.
* Slack notifications are sent when approvals are dismissed or overridden.

### Custom ownership

When using FlexReview Validation, you should move your Codeowners file to an Aviator Owners file to avoid GitHub overriding the validation settings. The Aviator Owners file is compatible with Codeowners, which means your reviewer requirements in Codeowners will be easily replicated in Aviator’s Custom Ownership model.

In addition, FlexReview also introduces concepts of [recursive ownership](https://docs.aviator.co/flexreview/concepts/recursive-ownership) that can help reduce the burden on the reviewers and authors. FlexReview Validation can be used without applying recursive ownership.

### **Selective Dismissal of Approvals**

GitHub provides an option to "Dismiss stale pull request approvals when new commits are pushed," but this can lead to unnecessary re-approvals from all reviewers even if only a small part of the code is changed. FlexReview’s Validation refines this process by:

* Identifying what changes have been made in the new commits.
* Dismissing only the approvals of reviewers whose owned files have changed.
* Keeping approvals intact for files that remain unchanged.

This approach minimizes redundant review requests while ensuring necessary approvals are always in place. Changes are determined on a per-commit basis - when a user approves a pull request, their approval is stored as an approval of the changes made in their owned files for that particular commit. When additional commits are made, the diff between these changes and the last approved commit are calculated for each approver. If there are any changes, that user’s approval is dismissed and they are notified to re-review.

## Scenarios

### **Validation Scenarios**

FlexReview applies smart validation in different ways based on what changes have been made.

**Merging in the base branch**

* No new approvals are required if no new changes are introduced.
* If a merge conflict occurred, these will be detected as a code change and some approvals may be reset.

**Files added or changed**

* Approvals are dismissed selectively.

**File deleted**

* Approval from the given file owner is dismissed.

**Base branch changes:**

* If the new base branch is also configured for FlexReview Validation, all approvals are dismissed.
* If the new branch is not configured for FlexReview Validation, approvals will not be dismissed.
* If using [aviator CLI](https://docs.aviator.co/aviator-cli) and this PR was part of a stack, new approvals will be calculated based on the target branch instead of the base branch and approvals will be selectively dismissed.
* If stacked PRs are created without using aviator CLI, they are considered independent changes and the validation workflow may will be applied as described above.

### Breakglass Scenarios

**Standard Flow**

1. A pull request is not yet fully approved by all required reviewers.
2. A user posts a “breakglass” comment on the PR.
3. A different user approves the PR.&#x20;
4. The PR is now eligible for merge.

**Self-Approval Issued After Breakglass**

1. The same user who posted the breakglass comment attempts to approve the PR.
2. Their approval does not count toward the required approvals.
3. The PR cannot be merged.

**New Commit After Approval**

1. A user approves the PR after the breakglass comment is issued.
2. A new commit is pushed to the PR before it is merged:
   1. If the commit is a rebase or does not change code:
      1. The previous approval still holds
      2. The PR can be merged.
   2. If the commit introduces actual code changes:
      1. The previous approval is invalidated.
      2. The PR cannot be merged without re-approval.

**Breakglass is Issued After PR Is Mergeable**

1. The PR is already eligible to be merged before a breakglass comment is added.
2. The breakglass comment has no impact.
3. The PR remains mergeable.

## **Why “Dismiss Approvals” is No Longer Needed**

GitHub’s "dismiss all approvals on push" rule is unnecessary with Validation, as FlexReview will dismiss approvals by comparing diffs at the per-file and per-commit level. This is done by:

1. Taking a diff of the base commit SHA and the head commit SHA for each file.
2. Evaluating if there are changes between the last approved commit for that file and the latest commit.
3. Dismissing the user approvals that have been invalidated while keeping all other approvals intact.

This ensures that approvals remain relevant and accurate throughout the PR lifecycle.

