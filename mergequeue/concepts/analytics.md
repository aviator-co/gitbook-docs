# Analytics

Analytics offers insights into the performance of Aviator and provides metrics on its usage. The following metrics are offered on the Analytics page of Aviator.

## **Time in Queue**

The time a PR spends in the queue before it is merged by the Aviator bot. The time is measured starting when the PR is queued (not labeled). This only includes the PRs that were merged by the Aviator bot. The metric is reported as: Average, p50 (50th percentile), p75 (75th percentile), p90 (90th percentile) and p100 (100th percentile).

![](</.gitbook/assets/Screen Shot 2022-05-17 at 3.36.03 PM.png>)

## Aviator **bot usage**

Represents how many PRs were merged by the bot vs. merged manually segmented by day.

## **PR failure reasons**

Gives you a better understanding of how many PRs are failing to automatically merge and why. The breakdown is done by Failure reasons. You can find more details in the [<mark style="color:blue;">Error Codes</mark>](../reference/status-codes.md) section.

The failed PR is counted multiple times if it failed multiple times in its lifecycle.

## **PR sync frequency**

This metric captures how many times the bot pulls the latest base branch into the current PR. This should only happen at most once if everyone uses the Aviator bot for merging the PRs.

## Learn More

* [Analytics API documentation](/mergequeue/reference/api-documentation.md#analytics)
