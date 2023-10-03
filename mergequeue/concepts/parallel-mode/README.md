# Parallel Mode

If you think sequential merging may not work for your use case, you can try Parallel mode. We designed Parallel mode with high output teams in mind where sequential continuous integration cycles may cause long wait cycles. This feature is only available in the Pro plan.

If you plan to set up parallel mode for your team, please reach out to [<mark style="color:blue;">howto@aviator.co</mark>](mailto:howto@aviator.co). We are offering free onboarding training for your entire engineering team, so your team can ask any questions they may have and understand all behaviors.

Parallel Mode requires good understanding of several concepts, as things work somewhat differently in parallel mode. The main principle is to run builds optimistically in parallel, and falling back when a failure occurs. To enable parallel mode, you can find a toggle option along with a few more settings on the Merge Rules page. We have described all the settings below.

## **Configuring Parallel mode**

Before configuring parallel mode, please read the entire documentation. In addition, please note a few things:

* Parallel mode uses Draft PRs to define parallel pipelines. Your repository must have the capability to support Draft PRs. On GitHub, draft PRs are supported on Team or Enterprise plans.
* The Github Mergeability check does not work on Draft PRs in Parallel mode, so you should explicitly specify the required checks for Draft PRs on the Status Check page. You will still need Github Mergeability checks to validate the original PR.
* At a minimum, we recommend setting the **max bot builds in parallel** configuration while enabling parallel mode. The rest of the settings correspond to advanced capabilities.

## **Draft PRs**

In parallel mode, the Aviator bot creates Draft PRs that combines changes from one or more queued PRs. These Draft PRs run the CIs in parallel to reduce the wait time.

When the first PR (let's say `PR #1`) is labeled with the trigger label, it behaves the same way as standard mode. The latest base branch is merged into the PR and a new CI is triggered.

When a second PR (`PR #2`) is labeled with the trigger label, the bot will create a new branch with changes from both `PR #1` and `PR #2`. Then a Draft PR is created from that new branch to trigger the CI runs. Now instead of monitoring the original PR, the Draft PR’s CI will be monitored.

For every subsequent PR labeled with the trigger label, the bot will pick up all the existing queued PRs along with the labeled one and create a new Draft PR. This ensures the validation is always performed on the most recent changes. In this way, each subsequent PR has an associated Draft PR that is created when the original PR was labeled.

![](<../../.gitbook/assets/Screen Shot 2022-05-17 at 3.19.23 PM.png>)

## **CI behaviors**

Due to the complexity of Parallel mode, we look into not just successful and failing CI modes, but also cases where the CI may get stuck.

### **CI completion**

When the CI for the Draft PR completes, the associated Draft PR itself is closed without merging, and the original PR is merged.

### **CI failure**

If the first Draft PR's CI fails and no other PR is queued, the original PR is dequeued.

Otherwise, in case the CI fails for the Draft PR on top of the queue, the bot closes all subsequent Draft PRs and restarts the queue after removing the failing PR.

### **CI stuck**

Let's say we have the following PRs:

`PR#2` (has an associated `Draft PR#3`)

`PR#4` (has an associated `Draft PR#5`)

In case a `Draft PR#3's`  CI gets stuck, but a subsequent `Draft PR#5's` CI completes, we assume eventual consistency. So the bot merges all PRs up to the PR with a completed Draft PR CI (both `PR#2` and `PR#4` will be merged), and the associated Draft PRs are closed.

In case the Draft PR CI passes, but the associated original PR's CI is stuck, the bot will flag that PR as stuck with the label set in the Merge Rules configuration (**Label to add if PR is stuck** ). You can also configure a stuck timeout (**Dequeue stuck PR after (mins)**), after which the PR will be reported as failed and the queue will be reset.

## **Managing parallelism**

Since every Draft PR triggers a new CI run, you may want to manage the parallelism for CI runs. You can set this in **Max bot builds in parallel**. The bot will pause queuing PRs once it hits the parallel limit. After that, the next one will only be created after one of the Draft PRs is closed.

Similar to capping the parallelism, you may need to pause queuing until a particular PR is merged. This may be needed if you have a long running CI that gets triggered by modifying specific files. Every subsequent Draft PR will also touch that file causing all subsequent CIs to be long running. In the Parallel mode configuration, you can specify a GitHub label to **Block further bot builds**. Once the specified GitHub label is added to a PR, no further Draft PRs will be built on top of it until that PR is merged or dequeued.

## **Resets**

There are a few ways in which Parallel Mode can be reset. One was the stuck behavior as described above. Here are a few more described below.

### **Manual reset**

If the queue is stuck, you may want to reset the queue. This can be done from the [<mark style="color:blue;">Queues page</mark>](https://mergequeue.com/queue/queued) by clicking "Reset Queue" button. This will close all existing draft PRs and requeue everything by creating new draft PRs.

### **Skip the line in parallel mode**

The skip the line label works the same way in parallel mode as in the standard mode, except in this case it will also reset all parallel PRs (similar to the manual reset). The PR that is labeled for skipping the line will move to the front and new parallel draft PRs are created.

### **Dequeuing a PR**

To dequeue a PR in Parallel Mode, you can remove the trigger label from the PR. In this case, all the Draft PRs that are in the queue after this PR are closed. The unlabeled PR is removed from the queue and the rest of the PRs are put back in the queue.

### Separate CI validations for draft PR

You can also configure separate CI validations for the original PRs and the draft PRs. This may be required if you want to either delay the full validation until the creation of draft PR or if you want to only run subset of tests in the draft PR.

#### Option A: Through Merge Rules page

You can customize the required checks for Parallel mode using the Merge Rules UI. In the parallel mode section of the rules, you can choose "Override required checks". Here you can opt in to use the same checks on draft PRs or select the custom checks.

![](<../../.gitbook/assets/Screen Shot 2022-07-18 at 10.12.09 AM (1).png>)

#### Option B: Through YAML config

These configurations can also be provided via a YAML config. To do so, you can specify `override_required_checks` in the `parallel_mode` section:

```yaml
  merge_mode:
    type: "parallel"
    parallel_mode:
      override_required_checks:
        - build_and_test
        - 'ci/circleci: build'
```

