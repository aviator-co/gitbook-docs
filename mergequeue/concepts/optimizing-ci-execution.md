---
description: >-
  Find out when Aviator MergeQueue automatically creates branches and pull
  requests in our guide. mq-bot-* branches and draft PRs, mq-tmp-* branches, and
  more.
---

# MQ Created Branches

In some situations, Aviator MergeQueue automatically creates branches and pull-requests. This page explains their purposes.

## mq-bot-\* branches and draft PRs

We create branches and draft PRs with `mq-bot-` prefix. When you enqueue a PR, they are re-tested with these branches. The branch contains changes from the latest base branch (e.g. `main`) and the enqueued PR.

## mq-tmp-\* branches

We create branches with `mq-tmp-` prefix for some purposes (e.g. checking if fast-forwardable). We do not create a pull-request on these branches and do not expect tests to run on this.

## CI configurations

As explained above, CI should run on `mq-bot-` branches, but not on `mq-tmp-` branches. Here are some CI configuration examples to exclude `mq-tmp-` from CI execution triggers.

### GitHub Actions

GitHub Actions can be configured to trigger based on the pull-request related events. Since we do not create a pull-request on `mq-tmp-` branches, using this pull-request trigger should suffice in most cases.

```yaml
name: test
on:
  pull_request
```

If you need to trigger on `push` for some reason, you can filter out at the job condition.

```yaml
name: test
on:
  push
jobs:
  unit-test:
    if: startsWith(github.head_ref,'mq-tmp-') != true
    runs-on: "ubuntu-latest"
```

### CircleCI

CircleCI supports ignoring specific branch prefixes.

```yaml
version: 2.1

workflows:
  version: 2
  build-and-test:
    jobs:
      - build-and-test:
          filters:
            branches:
              ignore:
                - /mq-tmp-.*/
```

### Buildkite

Buildkit supports ignoring specific branch prefixes. You can add `!mq-tmp-*` to the configuration to exclude those branches. See [<mark style="color:blue;">Branch configuration</mark>](https://buildkite.com/docs/pipelines/branch-configuration) for details.

### Others

Similarly, for other CI systems, you can optimize the CI by blacklisting the `mq-tmp-` feature branches. The configuration may vary depending on how it is set up. If you have questions regarding a specific CI, please reach out to us: [<mark style="color:blue;">howto@aviator.co</mark>](mailto:howto@aviator.co).\\
