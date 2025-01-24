---
description: >-
  Learn about expert scoring algorithms in FlexReview. Reviewer suggestions and
  expert review requirements are based on expert scores calculated for every
  user.
---

# Expert scoring algorithms

## Expert scores

FlexReview’s reviewer suggestion and expert review requirements are based on expert scores. The score is calculated for every user and for every file based on the file modification history.

<figure><img src="../../.gitbook/assets/Untitled (3) (1).png" alt="" width="416"><figcaption><p>Public domain: https://commons.wikimedia.org/wiki/File:ForgettingCurve.svg</p></figcaption></figure>

This score calculation logic takes into account many factors. However, we are taking a simple approach that is based on the [<mark style="color:blue;">Forgetting curve</mark>](https://en.wikipedia.org/wiki/Forgetting_curve). The forgetting curve shows how much information / memory is lost over the time in human’s memory. We apply this to the GitHub pull request code authoring and reviewing history. This allows us to build an estimator that reflects both recency and accumulated knowledge based on the past contributions.
