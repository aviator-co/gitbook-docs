# Orphan a Branch

Aviator CLI keeps track of all the branches you have created through the CLI or that you have adopted in a db file in `.git/av/av.db`. If for some reason you want to remove a stack from the CLI, you can run

```
git switch <branch>
```

and then run

```
av orphan
```

The current branch, and all branches following it in the stack, will be removed from `av.db` and will no longer be tracked by the CLI. To re-add them take a look at [Adopt a Branch](adopt-a-branch.md).
