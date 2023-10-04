# Priority merges

At times there are high priority changes that cannot wait for all the PRs ahead of this change in the queue. In Aviator, you can handle this by applying a skip\_line label. When this label is applied to a PR, Aviator will move this PR to the front of the queue to be merged first.

## Customizing the label

The default skip\_line label is called `mergequeue-priority`. This label can also be customized via the configuration file:

```
merge_rules:
  labels:
    trigger: "label_name"
    skip_line: "skip_line"
```

## Priority in parallel mode

When a skip\_line label is applied in parallel mode, all pending draft PRs are also closed and new draft PRs are constructed. Read more about this in our [<mark style="color:blue;">parallel mode documentation</mark>](https://docs.aviator.co/how-to-guides/parallel-mode). The behavior is also similar in fast forward mode or when using batching.

## Priority when using affected targets

When using affected targets, only the PRs that affect the same target as the skip\_line PR are deprioritized. All draft PRs associated with these deprioritized PRs will be closed, and the new skip\_line PR will be moved to the top of the queue. No other PRs in the queue are impacted.

## Instant merge

Instant merge is a merge method that Aviator merge the PR directly without waiting for the CI to finish. This requires elevated permissions for Aviator and can be configured using the Pilot workflow. Please see [the Pilot document](https://docs.aviator.co/pilot-automated-actions) on how to configure instant merge.

## FAQ

#### What happens if there is more than one high priority merge?

If there are additional high priority (skip\_line) PRs that are queued when one already exists, Aviator will queue the new high priority PRs right after the existing ones. This is similar to having a queue of high priority merges itself.

#### What happens if we remove the skip\_line label?

If the PR does not have the merge label, then the PR will be dequeued. Otherwise, if the PR is still queued the behavior varies. In default mode, the PR is moved back to the original position in the queue. For other modes, there is no change in the queue after removing the skip line label. This is to avoid unnecessary resets of existing CIs.

\
