---
description: >-
  Learn about reviewer suggestions and assignments in FlexReview. Reviewer
  suggestions reduce code review response time and help improve the developer
  workflow.
---

# Reviewer suggestion and assignment

## Goals

The ultimate goal with the reviewer suggestions is to reduce code review response time. FlexReview also has additional goals to help improve the developer workflow:

* Reduce the total number of reviewers needed for each code review, and therefore, reduce iteration time
* Knowledge sharing - expand the suggested pool of reviewers when applicable
* Ensure that the code owners requirement is satisfied, if a code owners file exist

## Analyzing past data

When you enable FlexReview on a repository, it starts in [<mark style="color:blue;">read-only mode</mark>](read-only-mode.md) and indexes the past pull requests data to calculate the domain expertise score of each author and reviewer for various code paths. It also captures the team memberships of the developers, and reads the existing `CODEOWNERS` file, or in a custom `.aviator/OWNERS`file.

## Calculating the suggestions

For detailed understanding of the calculations, please refer to the [<mark style="color:blue;">calculations algorithm</mark>](../reference/expert-scoring-algorithms.md). FlexReview takes into account a few factors to identify the right reviewers:

* Complexity of the code change - the complexity is calculated separately for each path in the given PR.
* Domain expertise score - this is calculated based on the past data of the code reviews. A user can gain domain expertise both by authoring the code as well as reviewing it. Read the algorithm for a detailed explanation of this calculation. Domain expertise score is calculated both for the author and the reviewer.
* Current review load of all valid reviewer candidates.
* Availability (timezone, OOO) - Not implemented yet.
* Ownership defined in the `CODEOWNERS` file - the suggester service will choose the least amount of reviewers possible for each code change in order to satisfy the `CODEOWNERS` requirement if the file exists.

Based on these heuristics, reviewers are suggested for a pull request.

* When the code complexity is high and the domain expertise of the author is low, a reviewer with high domain expertise will be chosen.
* When the code complexity is low and the domain expertise of the author is high, the pool of reviewers is increased so that non-experts may also be chosen based on the current review load, ownership, and availability.

## Reviewer Assignment

As part of reviewer assigned using Expert mode, Aviator also posts a comment on the GitHub pull request explaining the breakdown and reasoning for why that particular reviewer was assigned.

<figure><img src="../../.gitbook/assets/Screenshot 2025-01-07 at 3.56.16 PM.png" alt=""><figcaption></figcaption></figure>

The suggested reviewers are automatically assigned to the PR at the time of suggestion. If GitHub assigns reviewers based on `CODEOWNERS`, FlexReview will replace the assigned reviewers based on the suggested reviewers.

Note that if you are using the `CODEOWNERS`, then GitHub will automatically assign teams or specific reviewers. In this case, FlexReview can override the add or remove reviewers.

### Unowned file paths

If there are certain file paths that are unowned, that is, there is no rule in the CODEOWNERS file that matches the file. In such cases:

* If the pull request only contains the unowned file paths, no reviewers will be assigned.
* If an unowned file is modified along with some owned files, the reviewers of those owned files will be responsible for reviewing the unowned files.

### Fill the gap in CODEOWNERS

In general, we recommend relaxing `CODEOWNERS`and moving teams to `.aviator/OWNERS` unless you need a review strictly from that specific team. However, there are cases you do need such configuration.

Depending on the configured assignment method on FlexReview, it's possible that the assigned reviewer won't satisfy GitHub `CODEOWNERS`requirements. In that case, FlexReview adds extra reviewers to satisfy `CODEOWNERS`. This applies only for the teams that have FlexReview enabled. For the FlexReview disabled teams, a reviewer is not automatically assigned and you will need to assign a reviewer manually or other means.

### Multiple reviewer assignment

If a pull request touches multiple files that are owned by different teams, in some cases it is better to have multiple reviewers when they can collectively review the changes with the right context. FlexReview will generally choose the least amount of reviewers, but it assigns multiple reviewers in such situation.

For example, let's say we have following `.aviator/OWNERS`file:

```python
# .aviator/OWNERS
src             @acme-corp/engineering
src/ios         @acme-corp/ios-eng
src/ios/auth    @acme-corp/ios-auth-eng
src/ios/net     @acme-corp/ios-net-eng
```

and, we have users named `@alice`, who is in `ios-eng`and `ios-auth-eng` teams, and `@bob`, who is in `ios-eng`and `ios-net-eng` teams. When we have a PR that modifies files under `src/ios/auth` and `src/ios/net`, FlexReview will try to find a candidate from `ios-eng` team. `@alice` would know well about the auth directory, but not about the net directory. `@bob` would be the opposite. If we pick one reviewer from them, they might not know well on the other side of the directory.

FlexReview addresses this case by choosing multiple reviewers if one person would not cover the expertise well. It compares a case where one reviewer reviews all files and a case where multiple reviewers review individual files, and chooses the one that has a good score.
