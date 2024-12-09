# Rename a Branch

Branches created using `av branch` should only be renamed with `av branch` as well. `av` needs to update internal tracking metadata that defines the order of branches within a stack.

Simply use the `--rename` or `-m` flag.

```sh
aviator-co/av ‹original-branch-name*›$ av branch -m my-new-branch
aviator-co/av ‹my-new-branch*›$
```

If you have already created a pull request via `av pr`, `av` will not rename the branch without the `--force` flag.

```
aviator-co/av ‹test-pr*›$ av pr
Creating pull request for branch test-pr:
  - pushing to origin/test-pr
  - created pull request https://github.com/aviator-co/av/pull/175
aviator-co/av ‹test-pr*›$ av branch -m renamed-pr
Cannot rename branch test-pr: pull request #175 would be orphaned.
  - Use --force to override this check.
exit status 127
```

`av` cannot update the remote branch, but you can still force update your local branch name. The remote counterpart will still keep its old name.

```
aviator-co/av ‹test-pr*›$ av pr
Creating pull request for branch test-pr:
  - pushing to origin/test-pr
  - created pull request https://github.com/aviator-co/av/pull/175
aviator-co/av ‹test-pr*›$ av branch --force -m renamed-pr
aviator-co/av ‹renamed-pr*›$
```

{% hint style="info" %}
Note: Force updating the branch name requires closing the corresponding pull request and opening a new one using `av pr`. This will ensure that `av` can recognize the stack properly.
{% endhint %}
