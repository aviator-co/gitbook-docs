---
description: >-
  Read about the paused queues feature and how MergeQueue works when a queue is
  paused. Pausing a queue allows you to control whether devs can merge PRs.
---

# Paused Queues

There are cases where you want to pause the queue. For example, during the holiday season, you do not want to get PRs merged. Pausing a queue allows you to control whether devs can merge PRs.

You can pause queues per repository or per base branch (e.g. `main`). This allows you to pause the queue for the `release` branch, while allowing devs to merge PRs into `main`. The control is on the Web UI and provided via [API](../how-to-guides/pause-unpause-queues-via-api.md).

<figure><img src="../../.gitbook/assets/SCR-20241125-pvax.png" alt=""><figcaption></figcaption></figure>

When a queue is paused, MergeQueue works processes the PRs up to the merge timing. This includes creating a draft PR for [<mark style="color:blue;">the parallel mode</mark>](parallel-mode/). The [<mark style="color:blue;">instant merge action</mark>](priority-merges/instant-merges.md) also stops working when a queue is paused.

***

Deactivating a repository will halt most of the Aviator features on the repository. By deactivating a repository, any queuing actions won't be processed nor any comments are posted from Aviator. The only exception to this is changesets and you can create them in a deactivated repository.
