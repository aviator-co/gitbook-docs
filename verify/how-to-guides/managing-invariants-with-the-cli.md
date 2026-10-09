# Managing invariants with the CLI

You can review and manage your account's [invariants](../concepts/invariants.md) from the terminal with the [`aviator` CLI](../reference/cli.md). This guide covers what makes an invariant worth keeping, then the commands.

### What makes a good invariant

An invariant is a rule an LLM checks against what a pull request changed. Good ones need judgment about what the change does, like "New HTTP endpoints must enforce authentication before business logic."

* **One rule per invariant.**
* **Put scope in [conditions](../reference/invariant-conditions.md)**, not the body. An invariant is only considered for pull requests that change a file matching its conditions.
* **Carve out recurring exceptions, [waive](../concepts/invariants.md#waivers) rare ones.** Only name an exception in the body if it keeps coming up.

Verify picks which in-scope invariants apply by comparing each one's title, category, reason, and body to the change's spec and plan, so make the rule's subject clear in its title and body.

### Working with the CLI

Listing works with any API token. Creating or changing an invariant needs a user signed in with `aviator login` or a personal access token. Anyone can create one; editing, approving, rejecting, and deleting need a maintainer, an admin, or a member of the invariant's [owner team](../concepts/invariants.md#owner-teams).

| Command                                          | What it does                                                                                         |
| ------------------------------------------------ | ---------------------------------------------------------------------------------------------------- |
| `aviator invariants list`                        | List invariants, newest first. Filter with `--status`, `--repo`, `--source`, and `--ids`.            |
| `aviator invariants categories`                  | List the category slugs that `create` and `edit` accept.                                            |
| `aviator invariants create`                      | Create an invariant. Needs `--title`, `--category`, and `--body` or `--body-file`.                  |
| `aviator invariants edit <id>`                   | Change only the fields you pass.                                                                     |
| `aviator invariants approve <id>...`             | Activate invariants so Verify starts applying them.                                                   |
| `aviator invariants reject <id>...`              | Stop applying invariants but keep them on record.                                                    |
| `aviator invariants set-status <status> <id>...` | Set any status, for example to restore a rejected invariant to `pending`.                           |
| `aviator invariants delete <id>`                 | Delete an invariant permanently.                                                                     |

An invariant's status is `pending` (proposed, not applied), `active` (applied), or `rejected` (not applied, kept on record). An active invariant can also be turned off with `edit --disable`, which `list` shows as `active (disabled)`.

An invariant applies to every repository unless you pass `--repo owner/repo`, once per repository. Add conditions with `--condition`, once each:

| Flag                                         | Settings page group |
| -------------------------------------------- | ------------------- |
| `--condition 'file_path_glob=src/**/*.go'`   | Include files       |
| `--condition 'file_path_glob!=**/*_test.go'` | Exclude files       |
| `--condition 'language=go'`                  | Language            |

```bash
aviator invariants create \
  --title "HTTP handlers authenticate before business logic" \
  --category security \
  --body-file handler-auth.md \
  --condition 'file_path_glob=src/**/*.go' \
  --condition 'file_path_glob!=**/*_test.go'
```

Things to know:

* `create` activates the invariant immediately. Pass `--disable` to create it turned off.
* On `edit`, `--condition` and `--repo` replace the existing set, so pass all the ones you want to keep.
* `--enable` and `--disable` only apply to active invariants. Use `approve` for a pending one.
* `reason` explains why Aviator proposed the invariant. Verify reads it when picking invariants, and it can't be edited.
* Use `--body-file` to avoid shell quoting problems.
* `list` doesn't show bodies. Add `--json` to any command for the full response.
* `list` returns 20 invariants per page by default, up to 100 with `--limit`. Page with `--page` until `has_more` is false.

### See also

* [Invariant conditions](../reference/invariant-conditions.md): exact glob and language matching rules
* [Concepts: Invariants](../concepts/invariants.md): sources, categories, and waivers
* [Aviator CLI](../reference/cli.md): install and authentication
