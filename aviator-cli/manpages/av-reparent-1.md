---
description: >-
  Understand the av-reparent command for restructuring Git branches in Aviator
  CLI.
---

# av-reparent Command Guide

### NAME

av-reparent - Change the parent of the current branch

### SYNOPSIS

```synopsis
av reparent [--parent=<parent>]
```

### DESCRIPTION

This rebases the current branch onto the new parent and runs the restack operations on the children. It does not push the changes to the remote.

### OPTIONS

`--parent=<parent>` : Parent branch to rebase onto.
