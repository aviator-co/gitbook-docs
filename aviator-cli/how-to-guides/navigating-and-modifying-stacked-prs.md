# Navigating & Modifying Stacked PRs

### Visualizing a stack

Run the command

```
av tree
```

to print out a visualization of the current PR stack.

### Navigating a stack

Since a stack is (conceptually) a sequence of branches, navigating between different parts of the stack is as simple as running `git switch <branch name>`.

You can also use `av switch` to interactively switch the branches. Or you can use `av prev` and `av next` to navigate through branches in order.

### Adding a commit to a branch within a stack

First, checkout the branch you want to add a commit to with

```
git checkout "<branch name>"
```

(note that after you commit, subsequent branches in the stack will also contain this commit).

Modify the repository using your normal development workflow, and when you're done, stage and add the changes:

```
av commit -a -m "<msg>"
```

`av commit` will automatically rebase all of the commits in the stack to include the new commit, without pushing them to GitHub. If instead you run `git commit` you can always resync the stack with `av restack`.

To rebase and push your changes to GitHub run

```
av sync
```

