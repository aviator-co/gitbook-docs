# Add Commits in the Stack

You can create stacked PRs with Aviator CLI and send them for review. Your reviewer might request some code changes in one of the PRs in the middle of the stack. This how-to guide explains how to add a change in such cases.

## Setup

Let's use [<mark style="color:blue;">Hello-World</mark>](https://github.com/octocat/hello-world) repository as an example.

```bash
$ git clone https://github.com/octocat/hello-world hello-world
$ cd hello-world
$ av init
$ touch myfile
$ git add myfile
```

Then, create one branch.

```bash
# Using just 'av'
$ echo 1a >> myfile
$ av commit -A -m 1a --branch-name stack-1
$ echo 1b >> myfile
$ av commit -A -m 1b

# Or with a mix of 'av' and 'git'
$ av branch stack-2
$ echo 2a >> myfile
$ git commit -m 2a myfile
$ echo 2b >> myfile
$ git commit -m 2b myfile
```

This creates 2 branches.

```mermaid
%%{init: { 'gitGraph': {'showBranches': true, 'showCommitLabel':true,'mainBranchName': 'master'}} }%%
gitGraph
    commit id: "..."
    branch stack-1
    checkout stack-1
    commit id: "1a"
    commit id: "1b"
    branch stack-2
    checkout stack-2
    commit id: "2a"
    commit id: "2b"
```

## Changing the stack-1

We have two branches that correspond to two PRs; `stack-1` and `stack-2`. Let's assume that we need to change the `stack-1`. By running `av prev`, you can move back to the previous branch.

```
$ av prev
Checked out branch stack-1
```

You can switch the branch with `av switch`, `git switch`, or `git checkout` as well.

Now make a necessary change.

```
$ echo 1c >> myfile
$ av commit -A -m 1c
```

Currently the commit graph is like this:

```mermaid
%%{init: { 'gitGraph': {'showBranches': true, 'showCommitLabel':true,'mainBranchName': 'master'}} }%%
gitGraph
    commit id: "..."
    branch stack-1
    checkout stack-1
    commit id: "1a"
    commit id: "1b"
    branch stack-2
    checkout stack-2
    commit id: "2a"
    commit id: "2b"
    checkout stack-1
    commit id: "1c"
```

We want to make `stack-2` to be on top of the new commit 1c. To do this, run `av sync`.

```
$ av sync

  ✓ GitHub fetch is done
  ⣽ Restacking stack-2...

    * ⣽ stack-2 bca00e0
    │
    * ✓ stack-1 88e3595
    │
    * master 7fd1a60

  Rebase conflict while rebasing  stack-2
  Failed to rebase "refs/heads/stack-2" onto "refs/heads/stack-1" (merge base is "dd85e55")
  could not apply 8bf865c... 2a
  error: could not apply 8bf865c... 2a
  Recorded preimage for 'myfile'
  Could not apply 8bf865c... 2a


  Resolve the conflicts and continue the restack with av sync --continue
```

The file has a rebase conflict. Open `myfile` and resolve the conflict, and continue.

```
$ vim myfile
... Resolving conflict ...
$ git add .
$ av sync --continue

  ✓ Restack is done

    * ✓ stack-2 0be8a6e
    │
    * ✓ stack-1 88e3595
    │
    * master 7fd1a60

  ✓ Nothing to push to GitHub

    Following branches do not need a push.

      stack-1: No remote branch yet.
      stack-2: No remote branch yet.

  ✓ No merged branches to delete
```

Now the `stack-2` is rebased on top of the new commit 1c.

```mermaid
%%{init: { 'gitGraph': {'showBranches': true, 'showCommitLabel':true,'mainBranchName': 'master'}} }%%
gitGraph
    commit id: "..."
    branch stack-1
    checkout stack-1
    commit id: "1a"
    commit id: "1b"
    commit id: "1c"
    branch stack-2
    checkout stack-2
    commit id: "2a"
    commit id: "2b"
```

## Convenient CLI commands

When you need to change the commit in the middle of the stack, you almost always need to rebase to align the child branches. Aviator CLI has convenient commands, `av commit` and `av commit --amend` that runs `git commit` along with `av restack` afterwards.
