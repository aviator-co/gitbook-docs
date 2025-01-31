---
description: >-
  Access manual pages for the av-stack-next(1) command in Stacked PRs CLI
  documentation. This command will default to checking out the next branch in
  the stack.
---

# av-stack-next(1) in CLI

### NAME

av-next - Checkout the next branch in the stack

### SYNOPSIS

```synopsis
av next [<n> | --last]
```

### DESCRIPTION

Checkout a later branch in the stack. Without any options, this will default to checking out the next branch in the stack.

### OPTIONS

`<n>` : Checkout the branch that is `<n>` branches after the current branch in the stack.

`--last` : Checkout the last branch in the stack.

### SEE ALSO

`av-prev`(1)
