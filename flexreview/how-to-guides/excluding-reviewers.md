---
description: >-
  Learn how to exclude certain reviewers from being suggested. They will be
  still accepted for approval check validation but are never suggested by
  FlexReview.
---

# How to Exclude Reviewers

In a large organization, it may be desired to exclude certain reviewers from being suggested. These could be SREs, managers, product managers, designers or other inactive collaborators who may have access to the code base, and have contributed in the past but are passive collaborators now.&#x20;

The excluded reviewers are still accepted for approval check validation, but are never suggested by FlexReview.

To exclude the reviewers, go to [Repositories list view](https://app.aviator.co/repos), search for the repository that you want to change, and select FlexReview Status > Configure.

<figure><img src="../../.gitbook/assets/Screenshot 2025-01-07 at 3.29.56 PM.png" alt=""><figcaption><p>FlexReview Configure</p></figcaption></figure>

On this page, enter their GitHub handles on the FlexReview config page. Enter one username per line.

<figure><img src="../../.gitbook/assets/Screenshot 2025-01-07 at 3.28.46 PM.png" alt=""><figcaption><p>Reviewer Exclusion configuration</p></figcaption></figure>

For situations where you want reviewers not to just be excluded from automated reviewer suggestion but to be completely ignored by FlexReview, you can add them to the Ignore List. This is ideal for bot users that are set as a normal User account in GitHub.

On the same page as the Reviewer Exclusion you can open up the Ignore List and add one GitHub username per line:

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption><p>Ignore List configuration</p></figcaption></figure>
