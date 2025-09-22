# Stacked PRs

#### Remote agents create all changes as stacked PRs that makes it easy to update incrementally without needing to redo the whole stack. These remote agents leverage the [stacked PR CLI](../../../aviator-cli/) to perform these operations, you can also checkout an entire stack and make updates using the same CLI.

```
PR #1: Foundation Changes
  └─ PR #2: Core Implementation
      └─ PR #3: Test Updates
          └─ PR #4: Documentation
```
