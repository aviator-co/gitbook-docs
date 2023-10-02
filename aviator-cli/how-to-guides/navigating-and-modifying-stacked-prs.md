# Navigating & Modifying Stacked PRs

### Visualizing a stack

Run the command

```
av stack tree
```

to print out a visualization of the current PR stack.

### Navigating a stack

Since a stack is (conceptually) a sequence of commits, navigating between different parts of the stack is as simple as running `git checkout <branch name>`.

### Adding a commit to a branch within a stack

First, checkout the branch you want to add a commit to with

```
git checkout "<branch name>"
```

(note that after you commit, subsequent branches in the stack will also contain this commit).

Modify the repository using your normal development workflow, and when you're done, stage and add the changes:

```
git add -A .
git commit
```

Run the

```
av stack sync
```

command to synchronize all the subsequent branches in the stack with their parent branches. This will require you to resolve any conflicts that occur along the way.
