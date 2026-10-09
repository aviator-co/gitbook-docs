---
description: >-
  Find out how the priority merge feature works. When this label is applied to a
  pull request, Aviator moves it to the front of the queue to be merged first.
---

# Priority Merges

At times there are high priority changes that cannot wait for all the pull requests ahead of this change in the queue. With Aviator, you can handle this by applying a `skip_line` label. When this label is applied to a pull request, Aviator will move this pull request to the front of the queue to be merged first.

## Customizing the label

The default `skip_line` label is `mergequeue-priority`. This label can also be customized via `merge_rules.labels` in [<mark style="color:blue;">the YAML config</mark>](https://app.aviator.co/schema/index.html#aviator_config_yaml.json):

```
merge_rules:
  labels:
    trigger: "label_name"
    skip_line: "skip_line"
```

## Priority in parallel mode

When a `skip_line` label is applied in a repository using the [<mark style="color:blue;">parallel queue mode</mark>](../queue-modes.md#parallel-mode), all pending bot pull requests are also closed and new bot pull requests are constructed. Read more about this in our [<mark style="color:blue;">parallel mode documentation</mark>](https://docs.aviator.co/how-to-guides/parallel-mode). The behavior is also similar when using fast-forwarding or when using batching.

## Priority when using affected targets

When using affected targets, only the pull requests that affect the same target as the `skip_line` pull request are de-prioritized. All bot pull requests associated with these de-prioritized pull requests will be closed, and the new `skip_line` pull request will be moved to the top of the queue. No other pull requests in the queue are impacted.

## Priority without resetting the queue

By default, a `skip_line` pull request in parallel mode resets the queue as described above, and Aviator starts its draft PR even when `max_parallel_builds` has been reached. To avoid the reset, enable `non_destructive_skip_line` under `parallel_mode`. It is disabled by default.

```yaml
merge_rules:
  merge_mode:
    type: parallel
    parallel_mode:
      non_destructive_skip_line: true
```

With this setting enabled:

* Aviator does not close draft PRs that are already running. They finish CI and merge as normal.
* Aviator picks the `skip_line` pull request ahead of all other queued pull requests for the next draft PR, and builds that draft PR on top of the running ones.
* Aviator respects `max_parallel_builds` for `skip_line` pull requests. If the queue is at capacity, the `skip_line` pull request waits for a build slot to free up.
* When using affected targets, the `skip_line` pull request depends on the earlier running draft PRs that share its targets.

The trade-off is that a `skip_line` pull request merges after the draft PRs that were already running, rather than right away.

## Instant merge

Instant merge is a merge method that Aviator merge the pull request directly without waiting for the CI to finish. This requires elevated permissions for Aviator and can be configured using the Pilot workflow. See the [<mark style="color:blue;">Pilot documentation</mark>](https://docs.aviator.co/pilot-automated-actions) to learn how to configure instant merges.

## FAQ

### What happens if there is more than one high priority merge?

Aviator will queue new `skip_line` pull requests after the existing ones (but before any non-`skip_line` pull requests).

### What happens if the `skip_line` label is removed?

If the pull request does not have the merge label, then the pull request will be dequeued. Otherwise, if the pull request is still queued, the behavior varies. For repositories using the [<mark style="color:blue;">sequential queue mode</mark>](../queue-modes.md#sequential-mode), the pull request is moved back to the original position in the queue. For other modes, there is no change in the queue after removing the skip line label. This prevents causing additional [<mark style="color:blue;">queue resets</mark>](../parallel-mode/#resets).
