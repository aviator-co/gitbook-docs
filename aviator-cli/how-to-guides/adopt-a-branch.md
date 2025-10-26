---
description: >-
  Documentation on adopting a branch with av CLI from Aviator with the command
  av branch.
---

# How to Adopt a Branch in CLI

Aviator CLI needs to maintain the metadata for branches so that it can remember the parent-child relationships among them. If you create branches with `av branch`, the metadata is created upon branch creation. You can attach the metadata for branches that are created with `git branch`.

## Create a repository

We will use an example repository from [<mark style="color:blue;">https://github.com/octocat/hello-world</mark>](https://github.com/octocat/hello-world). Clone it and initialize the Aviator CLI.

```
$ git clone https://github.com/octocat/hello-world
Cloning into 'hello-world'...
remote: Enumerating objects: 13, done.
remote: Total 13 (delta 0), reused 0 (delta 0), pack-reused 13
Receiving objects: 100% (13/13), done.
$ cd hello-world
$ av init
Successfully initialized repository for use with av!
```

## Creating a branch outside of av

Let's say instead of using `av branch`, we used a normal Git command to create a branch and a commit.

```
$ git switch --create mytopic
Switched to a new branch 'mytopic'
$ git commit --allow-empty --message="New commit"
[mytopic d384683] New commit
```

Now we can see, the newly created branch `mytopic` is not tracked in `av tree`, but it's shown in `git branch`.

```
$ av tree
$ git branch -vv
  master  7fd1a60 [origin/master] Merge pull request #6 from Spaceghost/patch-1
* mytopic d384683 New commit
```

## Adopting a branch with av adopt

By running `av adopt` specifying the parent branch (in this case `master`) adds metadata for the current branch `mytopic`. Now, if you run `av tree`, you can see that `mytopic` is correctly recognized as a child of `master`.

```
$ av adopt
Choose which branches to adopt (Use space to select / deselect).
  * mytopic (HEAD, chosen for adoption)
  │   New commit
  │
  * master
```

## Adopting an existing PR

If you have also created a PR without using `av`, you can also synchronize your local `av tree` to adopt that PR. To do so, first adopt the branch as described above and then run `av sync` .

```
$ av adopt
Choose which branches to adopt (Use space to select / deselect).
  * mytopic (HEAD, chosen for adoption)
  │   New commit
  │
  * master  
$ av sync
  ✓ GitHub fetch is done
  ✓ Restack is done

    * mytopic-testing 6829015
    │
    * master fbdf1f6

  ✓ Pushed to GitHub

    Following branches are pushed.

      mytopic-testing
        Remote: 6829015 New commit 2025-10-26 11:36:18 -0700 -0700 (2 minutes ago)
               Parent=
        Local:  6829015 New commit 2025-10-26 11:36:18 -0700 -0700 (2 minutes ago)
               Parent=master
        PR:     https://github.com/aviator-co/mergeit/pull/9914

  ✓ No merged branches to delete
```
