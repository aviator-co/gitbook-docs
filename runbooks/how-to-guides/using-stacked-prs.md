# Stacked PRs execution

Remote agents create all changes as stacked PRs that makes it easy to update incrementally without needing to redo the whole stack. These remote agents leverage the [stacked PR CLI](../../../aviator-cli/) to perform these operations, you can also checkout an entire stack and make updates using the same CLI.

```
PR #1: Foundation Changes
  └─ PR #2: Core Implementation
      └─ PR #3: Test Updates
          └─ PR #4: Documentation
```

### How stacked PRs work in Runbooks

By default, each step in the Runbook creates a separate pull request that builds upon the previous one. All the substeps with the step add commits to the parent step within the same PR. So for instance, if your Runbooks looks like the folllowing:

```
## Step 1
#### 1.1
#### 1.2
### Step 2
#### 2.1
```

then your will have two separate PRs such that:

```
main <- step-1-pr <- step-2-pr
```

Here `step-1-pr` contains two commits (`1.1` and `1.2`), and `step-2-pr` contains one commit (`2.1`). Any revisions through GitHub comments are appended as additional commits to existing branches.

This dependency chain ensures that changes are applied in the correct order and that each step has access to the modifications made by previous steps.

### Branch management strategy

Runbooks automatically manage the Git branching strategy for stacked PRs. Each step creates a uniquely named branch following the pattern `av/rb-{session-id}-{step-number]-{hash}` to avoid conflicts and provide clear traceability back to the specific Runbook execution.

The Runbook execution engine handles all Git operations including branch creation, commits, and PR creation. Developers don't need to manually manage branches or worry about merge conflicts between steps, as the system ensures each step starts from a clean state based on the previous step's completed changes.

### Step isolation and dependency management

Each step in a stacked PR workflow operates in isolation while having access to the cumulative changes from all previous steps. This isolation ensures that step failures don't corrupt the state of other steps and that individual steps can be retried or modified without affecting the entire workflow.

For complex workflows with conditional logic, the system can create branching stacks where certain PRs are only created if specific conditions are met. This allows Runbooks to implement sophisticated automation logic while maintaining the reviewability of each individual change.

### Review and approval workflow

[Stacked PRs](https://www.aviator.co/blog/stacked-prs-code-changes-as-narrative/) enable a granular review process where each step can be reviewed and approved independently. Reviewers can focus on the specific changes introduced by each step without being overwhelmed by the entire automation workflow at once.

The Runbook execution engine coordinates the review process by ensuring that PRs are presented to reviewers in the correct order.

Automated checks and CI processes run independently for each PR in the stack, providing step-level validation and ensuring that each modification maintains code quality and doesn't introduce regressions.

### Merge coordination

PRs should be merged in dependency order, starting from the base of the stack and progressing through each step until all changes are integrated into the target branch.

Stacked PRs from Runbook executions integrate seamlessly with [Aviator's MergeQueue](../../../mergequeue/). The merge queue ensures that stacked PRs are processed in the correct order and that the final integration doesn't conflict with other concurrent changes to the codebase.

The merge queue validation runs comprehensive checks across the entire stack before allowing any PRs to merge, ensuring that the complete automation workflow will integrate cleanly with the current state of the target branch.

This integration provides additional safety guarantees for automated code modifications while maintaining the development team's existing merge queue workflows and policies.

### Tracking progress

The Runbook execution dashboard provides real-time visibility into the stacked PR workflow, showing the status of each PR, review progress, and merge coordination. Developers can track the progress of complex automation workflows and intervene when necessary.

Each PR in the stack includes metadata linking it back to the specific runbook execution and step, making it easy to understand the context and purpose of automated changes. This traceability is essential for debugging issues and understanding the impact of runbook executions on the codebase.

<figure><img src="../../../.gitbook/assets/Screenshot 2025-10-29 at 8.44.59 AM.png" alt=""><figcaption></figcaption></figure>

### AttentionSet

The system also provides notifications and alerts when stacked PRs require attention, such as when reviews are needed or when merge conflicts need manual resolution.
