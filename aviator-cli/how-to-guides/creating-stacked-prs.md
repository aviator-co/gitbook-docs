---
description: Documentation on creating Stacked PRs with av CLI from Aviator.
---

# How to Create Stacked PRs in CLI

### Creating a Branch

Run the command

```
av branch <name>
```

to create a new branch.

If you're currently on the trunk branch of your repository (usually `main` or `master`), this will create a new stack. Otherwise, the command will create a branch that is stacked on top of your checked-out Git branch before running the command. You can stack a branch even if the first (root) branch wasn't created with the `av branch` command.

### Creating a Branch and Committing

Alternatively, you can take advantage of

```
av commit -b -m "<message>"
```

This will create a new branch, with an automatically generated branch name based on the commit message, and then commit the changes to the new branch with the given message.

### Creating a Pull Request

Create the stacked PR using the following command:

```
av pr
```

By default, this will create the PR title and body based on the headline and message of the first commit in the branch (see `av pr --help` for details on how to override this).

To create PRs for each branch in the stack you can do `av pr --all` . Alternatively, you can run `av pr --all --current` to only create PRs up to branch you currently have checked out in the stack.

You can also [adopt a PR created without using the CLI](adopt-a-branch.md#adopting-an-existing-pr).&#x20;
