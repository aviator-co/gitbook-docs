---
description: >-
  Find answers to common questions about the ChangeSets feature that creates a
  list of PRs to be merged.
---

# FAQs

<details>

<summary>Can a single PR be part of multiple ChangeSets?</summary>

Yes, technically you can add a PR to multiple ChangeSets, but it will be merged with the first ChangeSet that is flagged for merging.

</details>

<details>

<summary>Can a single ChangeSet have multiple PRs from the same repository?</summary>

Yes, ChangeSets can have multiple PRs in the same repository. If using global CI validation, you will have to handle incorporating changes from multiple PRs/branches in the same repository yourself.

</details>

<details>

<summary>Can more PRs be added to the ChangeSet once "Start CI" is triggered?</summary>

Yes, if a new PR is added to the ChangeSet, the CI webhook will be retriggered with a new Status Run ID.

</details>

<details>

<summary>Can I review all previous the check run statuses?</summary>

Yes, the Aviator web app shows links to all previous status checks. Only the latest status run is considered when validating the ChangeSet for merge.

</details>
