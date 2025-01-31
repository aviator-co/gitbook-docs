---
description: >-
  Find and fix issues with automatic reviewer assignment in FlexReview using
  this troubleshooting guide.
---

# Troubleshoot Reviewer Assignment

## A reviewer is not assigned to a PR

Make sure you have read the [whitelist Teams](whitelist-teams-for-review-assignment.md) for reviewer assignment. The most common reasons why a reviewer may not be assigned:

1. Do you use a CODEOWNERS file? FlexReview currently requires presence of a CODEOWNERS or an `.aviator/OWNERS`file to suggest reviewers.
2. Is FlexReview global enabled for this repository? From the menu, go to Repositories and check the FlexReview status for the repository. Enable it if it's not already enabled. It may take a few minutes for Aviator to index the pull request data after enabling FlexReview global config.
3. Are the right teams enabled? Even after the global config, FlexReview requires each team to be separately enabled as well. Go to Teams section, and select your GitHub Team from the dropdown. See if this is enabled. Make sure at least some of the modified files in the pull request are owned by this team.
4. Are the authors whitelisted? This is enabled for everyone by default. Go to FlexReview configuration (Repositories > FlexReview status > Configure) for the given repository, make sure that the list of GitHub users here include the team or the user who authored this pull request.

## No owners / unowned files

There may be a scenario where no CODEOWNERS path matches the files that are modified. These files are considered unowned. In the scenario where all files in the pull request are unowned, Aviator will not assign any reviewers.
