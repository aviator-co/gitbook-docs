# Creating Stacked PRs

### Creating a Branch

Run the command

```
av stack branch "<branch name>"
```

to create a new branch.

If you're currently on the trunk branch of your repository (usually `main` or `master`), this will create a new stack. Otherwise, the command will create a branch that is stacked on top of your checked-out Git branch before running the command. You can stack a branch even if the first (root) branch wasn't created with the `av stack branch` command.

### Creating a Pull Request

Pull requests for stacked PRs should always be created with the

```
av pr create
```

command.

By default, this will create the PR title and body based on the headline and message of the first commit in the branch (see `av pr create --help` for details on how to override this).

Currently, each PR in the stack must be created individually (by checking out the branch with `git checkout "<branch name>"` and `av pr create`).

