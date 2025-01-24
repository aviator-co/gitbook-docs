---
description: >-
  View documentation for TestDeck dashboard using this guide. Learn about
  historical view, build reports, and test suite history.
---

# TestDeck Dashboard

## Historical view

A golden branch is the protected base branch (typically referred to as `master` or `main`) that is assumed to be always passing. Any CI failures on this branch are deemed flaky. Occasional true failures in this branch can be manually flagged as such to keep data coherent.

Aviator reports the results of CI checks in a historical view, and provides stats around destabilization of builds. You can easily monitor the status of both the repo's golden branch and all other branches.

<figure><img src="../.gitbook/assets/Screen Shot 2023-03-23 at 2.59.33 PM.png" alt=""><figcaption></figcaption></figure>

## Build reports

Other useful details include history of a specific test suite, as well as a particular build.

<figure><img src="../.gitbook/assets/Screen Shot 2023-03-23 at 2.59.04 PM.png" alt=""><figcaption></figcaption></figure>
