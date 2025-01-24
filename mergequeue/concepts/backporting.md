---
description: >-
  Read how backporting works with Aviator MergeQueue. Backport a particular PR
  on another base branch to create a new PR that keeps and applies original
  changes.
---

# Backporting

In many development workflows,developers may create separate stable branches for release candidates that are different from the trunk or default branch. In such cases, the branches may diverge significantly if they are long lived.

For instance, if there is a release candidate branch called release-v3.4 where a bug was found, the developer may prepare a bug fix for this release branch, but now this bug fix should be made in the other branches as well.

This can be easily done using backporting. You can tell Aviator to backport a particular PR on another base branch. Aviator will create a new PR that cherry picks the changes from the original PR and applies on the specified base branch.
