# Invariant conditions

Conditions decide which pull requests an [invariant](../concepts/invariants.md) is eligible for. Verify checks them against the pull request's changed file paths before the selector runs. An eligible invariant is only a candidate, and the selector still decides whether it applies to the change. An invariant with no conditions is eligible for every pull request.

This page is the exact matching behavior, with examples you can check your own conditions against.

### The rules

Conditions are set in three groups:

| Group             | Condition type   | A file passes when it matches |
| ----------------- | ---------------- | ----------------------------- |
| **Include files** | `file_path_glob` | any of the globs              |
| **Exclude files** | `file_path_glob` | none of the globs             |
| **Language**      | `language`       | any of the languages          |

1. Conditions are tested against each changed file on its own. The invariant is eligible when at least one changed file is in scope.
2. A file is in scope when it passes every group that has at least one condition. An empty group is skipped.
3. Within a group it's OR. Across groups it's AND. A file matching Exclude files is always out of scope.
4. With only Exclude files set, every file that isn't excluded is in scope.
5. Languages can only be included. There's no way to exclude a language.

Because every file is tested on its own, one file can't satisfy Include files while a different file satisfies Language. A single file has to pass every group.

### File path globs

Globs use `.gitignore` syntax and are matched against the file's path relative to the repository root, for example `src/api/users.py`.

| Syntax           | Meaning                                                                                       |
| ---------------- | --------------------------------------------------------------------------------------------- |
| `*`              | Any characters within one path segment. Doesn't cross `/`.                                    |
| `?`              | Exactly one character, not `/`.                                                               |
| `[abc]`, `[a-z]` | One character from the set or range.                                                          |
| `**`             | Any number of directories, including none.                                                    |
| No `/` in value  | Matches the name at any depth. `*.py` covers `a.py` and `src/deep/a.py`.                      |
| Leading `/`      | Anchors to the repository root. `/migrations/` doesn't match `app/migrations/0001.py`.        |
| Trailing `/`     | Matches everything inside a directory with that name.                                        |
| `\`              | Escapes the next character. `\#notes.md` matches a file literally named `#notes.md`.          |

#### Examples

| Glob             | Path                     | Match | Why                                                  |
| ---------------- | ------------------------ | ----- | ---------------------------------------------------- |
| `src/*.py`       | `src/api.py`             | yes   |                                                      |
| `src/*.py`       | `src/api/users.py`       | no    | `*` doesn't cross `/`.                               |
| `src/**/*.py`    | `src/api.py`             | yes   | `**` can match zero directories.                     |
| `src/**/*.py`    | `src/api/users.py`       | yes   |                                                      |
| `src/**/*.py`    | `lib/src/api.py`         | no    | A glob containing `/` is anchored to the root.       |
| `**/src/**/*.py` | `lib/src/api.py`         | yes   |                                                      |
| `*.py`           | `src/deep/a.py`          | yes   | No `/` in the glob, so it matches at any depth.      |
| `*.test.ts`      | `web/x/a.test.ts`        | yes   |                                                      |
| `Dockerfile`     | `svc/Dockerfile`         | yes   |                                                      |
| `Dockerfile`     | `Dockerfile.dev`         | no    | The whole name has to match.                         |
| `migrations`     | `app/migrations/0001.py` | yes   | A bare name also matches a directory at any depth.   |
| `/migrations/`   | `app/migrations/0001.py` | no    | Leading `/` anchors to the root.                     |
| `/migrations/`   | `migrations/0001.py`     | yes   |                                                      |
| `src/api/`       | `src/api/users.py`       | yes   |                                                      |
| `src/api/`       | `src/apiv2/x.py`         | no    | Directory names match whole, not as a prefix.        |
| `docs/**`        | `docs/x/y/a.md`          | yes   |                                                      |
| `src/?.py`       | `src/ab.py`              | no    | `?` is exactly one character.                        |
| `*.[jt]s`        | `a.ts`                   | yes   |                                                      |
| `*.[jt]s`        | `a.jsx`                  | no    |                                                      |

#### Common mistakes

| Glob                      | What happens                              | Use instead                                            |
| ------------------------- | ----------------------------------------- | ------------------------------------------------------ |
| `*.{ts,tsx}`              | Matches nothing. Braces aren't expanded.  | Two includes, `*.ts` and `*.tsx`.                      |
| `./src/*.py`              | Matches nothing. Paths never start with `./`. | `src/*.py`                                         |
| `SRC/*.py`                | Doesn't match `src/a.py`. Matching is case sensitive. | `src/*.py`                                     |
| `src/*.py` for all Python | Misses files in subdirectories.           | `src/**/*.py`                                          |
| `!src/generated/**`       | Rejected on save.                         | `src/generated/**` under Exclude files.                |

### Languages

A `language` condition matches a tag derived from the file's name and extension. Verify doesn't read file contents, so a file like `bin/deploy` with no recognized extension or name has no language tag. Values are case insensitive and saved in lowercase. A value that no filename can produce is rejected on save.

| File                          | Tag to use   | Notes                                              |
| ----------------------------- | ------------ | -------------------------------------------------- |
| `.py`                         | `python`     | Not `py`. Stub files (`.pyi`) are `pyi`.           |
| `.ts`                         | `ts`         | Not `typescript`. Doesn't cover `.tsx`.            |
| `.tsx`                        | `tsx`        |                                                    |
| `.js`, `.mjs`, `.cjs`         | `javascript` | Doesn't cover `.jsx`.                              |
| `.jsx`                        | `jsx`        |                                                    |
| `.go`                         | `go`         |                                                    |
| `.rs`                         | `rust`       |                                                    |
| `.java`                       | `java`       |                                                    |
| `.kt`                         | `kotlin`     |                                                    |
| `.rb`                         | `ruby`       |                                                    |
| `.cs`                         | `c#`         |                                                    |
| `.c`                          | `c`          |                                                    |
| `.cc`, `.cpp`                 | `c++`        |                                                    |
| `.h`                          | `c`, `c++`   | Headers carry both tags, plus `header`.            |
| `.sh`                         | `shell`      |                                                    |
| `.sql`                        | `sql`        |                                                    |
| `.tf`                         | `terraform`  |                                                    |
| `.yaml`, `.yml`               | `yaml`       |                                                    |
| `.json`                       | `json`       |                                                    |
| `.md`                         | `markdown`   |                                                    |
| `.proto`                      | `proto`      |                                                    |
| `.graphql`                    | `graphql`    |                                                    |
| `Dockerfile`, `Dockerfile.*`  | `dockerfile` |                                                    |

Tags come from the [identify](https://github.com/pre-commit/identify) library, the same one pre-commit uses for its `types` filter. Any tag it assigns from a filename is valid. Most files also carry a generic `text` tag, so `language: text` makes nearly every file eligible.

To cover a language family, add one include per tag. TypeScript is `ts` and `tsx`, JavaScript is `javascript` and `jsx`.

### Worked examples

Each example lists the conditions and then how different pull requests resolve.

#### Go code, but not Go tests

| Group         | Value          |
| ------------- | -------------- |
| Include files | `src/**/*.go`  |
| Exclude files | `**/*_test.go` |

| Changed files                                  | Eligible | Why                                                  |
| ---------------------------------------------- | -------- | ---------------------------------------------------- |
| `src/api/handler.go`                           | yes      |                                                      |
| `src/api/handler_test.go`                      | no       | Excluded.                                            |
| `src/api/handler_test.go`, `README.md`         | no       | `README.md` isn't under `src/` or a `.go` file.     |

#### TypeScript in the web app

| Group         | Value    |
| ------------- | -------- |
| Include files | `web/**` |
| Language      | `ts`     |
| Language      | `tsx`    |

| Changed files                          | Eligible | Why                                                                          |
| -------------------------------------- | -------- | ---------------------------------------------------------------------------- |
| `web/app/page.tsx`                     | yes      | `tsx` and under `web/`.                                                      |
| `web/app/page.css`                     | no       | Under `web/`, but not `ts` or `tsx`.                                         |
| `api/types.ts`                         | no       | `ts`, but not under `web/`.                                                  |
| `web/app/page.css`, `api/types.ts`     | no       | Each file passes only one group. A single file has to pass both.             |

#### Python outside migrations

| Group         | Value         |
| ------------- | ------------- |
| Exclude files | `migrations/` |
| Language      | `python`      |

| Changed files                                   | Eligible | Why                                          |
| ----------------------------------------------- | -------- | -------------------------------------------- |
| `app/migrations/0042.py`                        | no       | Excluded.                                    |
| `app/models.py`, `app/migrations/0042.py`       | yes      | `app/models.py` is in scope.                 |

#### Anything except docs

| Group         | Value   |
| ------------- | ------- |
| Exclude files | `docs/` |

| Changed files              | Eligible | Why                                                       |
| -------------------------- | -------- | --------------------------------------------------------- |
| `docs/a.md`                | no       | The only changed file is excluded.                        |
| `docs/a.md`, `src/x.py`    | yes      | Include files is empty, so `src/x.py` is in scope.        |

### Save errors

Conditions are validated when you save the invariant.

| Error                                                   | Cause                                                                       |
| ------------------------------------------------------- | --------------------------------------------------------------------------- |
| Condition value cannot be empty.                        | Blank value, including only whitespace.                                     |
| A file path can't start with `!`.                       | Add the glob without `!` under Exclude files instead.                       |
| This file path isn't a usable pattern.                  | An unclosed `[`, or a value starting with `#`, which `.gitignore` treats as a comment. Escape a literal `#` as `\#`. |
| Unknown language tag.                                   | The value isn't a tag any filename produces, for example `typescript`.      |
| Remove duplicate conditions before saving.              | The same value appears twice in one group.                                  |
| A file path can't be both included and excluded.       | The same glob is under both Include files and Exclude files.                |

### See also

* [Concepts: Invariants](../concepts/invariants.md)
* [Setting up org invariants](../setting-up-org-invariants.md)
* [Managing invariants with the CLI](../how-to-guides/managing-invariants-with-the-cli.md)
