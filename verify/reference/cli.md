# Aviator CLI

The `aviator` CLI submits intent and acceptance criteria to Verify from your terminal or from inside a coding agent. It is the preferred way to talk to Verify.

This is a different tool from `av`, the [Stacked PRs CLI](../../aviator-cli/). The two are installed separately and don't depend on each other.

### Install

```bash
brew tap aviator-co/tap
brew install aviator-co/tap/aviator
```

Linux users can install the `.deb` or `.rpm` package from the [releases page](https://github.com/aviator-co/aviator-cli/releases), or download the archive for their platform.

Confirm the install:

```bash
aviator version
```

### Authentication

#### OAuth (Preferred)

```bash
aviator login
```

#### API Token

The CLI reads an API token from the `AVIATOR_API_TOKEN` environment variable:

```bash
export AVIATOR_API_TOKEN=<your token>
```

Create a User Access Token at [app.aviator.co/settings/personal/api\_token](https://app.aviator.co/settings/personal/api_token). Submissions are attributed to the user the token belongs to, so each person needs their own — a shared token collapses the audit trail.

You can also put the token in a config file. The CLI reads a `config.yaml` (`.json` and `.toml` also work) from the first of these that exists:

1. `$XDG_CONFIG_HOME/aviator/`
2. `~/.config/aviator/`
3. `~/.aviator/`
4. `$AVIATOR_HOME/`, if that variable is set

```yaml
aviator:
  apiToken: <your token>
```

A repo-local `.git/aviator/config.yaml` is merged on top of the global one, and environment variables override both.

For on-premise installations, point the CLI at your instance with `AVIATOR_API_HOST` (or `aviator.apiHost` in the config file). It defaults to `https://api.aviator.co`.

### Commands

| Command           | What it does                                                                                                                                                    |
| ----------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `aviator verify`  | Submit intent and acceptance criteria for a change you're writing yourself.                                                                                     |
| `aviator runbook` | Create a runbook and have Aviator's agent implement the change.                                                                                                 |
| `aviator sessions` | List your reviews in a repo, or find the one on a branch or PR. See [`aviator sessions`](#aviator-sessions).                                                  |
| `aviator show`    | Show a review, its criteria, its latest verification, and its recent runs, e.g. `aviator show r/123`.                                                           |
| `aviator results` | Show the failures from a review's latest verification, or from an earlier run with `--run`.                                                                     |
| `aviator edit`    | Update a review's intent or replace its acceptance criteria. See [`aviator edit`](#aviator-edit).                                                               |
| `aviator dismiss` | Delete acceptance criteria or waive failing invariants on a review. See [`aviator dismiss`](#aviator-dismiss).                                                   |
| `aviator scenarios` | Show what a verification run exercised and the evidence it captured. Defaults to the latest run.                                                              |
| `aviator evidence` | Download one piece of evidence, such as a screenshot or a scenario's trace.                                                                                    |
| `aviator init`    | Set up your coding agents to capture intent before a PR. See [Set up agent hooks](../how-to-guides/set-up-agent-hooks.md).                                      |
| `aviator hooks`   | Manage the hooks `init` installed — `aviator hooks uninstall` removes them.                                                                                     |
| `aviator decisions` | Read and record a review's [decisions](../concepts/decisions.md). See [`aviator decisions`](#aviator-decisions).                                            |
| `aviator invariants` | Manage your account's [invariants](../concepts/invariants.md).                                                                                               |
| `aviator version` | Print the CLI version.                                                                                                                                          |

`verify`, `runbook`, `sessions`, `show`, `results`, `edit`, `dismiss`, `scenarios`, `decisions`, and `invariants` take `--json` to print a single JSON object instead of the human summary. Reviews are identified by `id` (for example `r/123`) and `url`.

### `aviator sessions`

Lists your reviews in a repo, newest first. Check here before running `aviator verify`, so you don't create a second review for a branch that already has one.

```bash
aviator sessions --repo myorg/myrepo --branch add-rate-limiting
aviator sessions --repo myorg/myrepo --pr 1234
```

| Flag       | Required | Description                                          |
| ---------- | -------- | ---------------------------------------------------- |
| `--repo`   | yes      | GitHub repo as `owner/repo`.                         |
| `--branch` | no       | Only reviews on this working branch.                 |
| `--pr`     | no       | Only reviews linked to this PR number.               |
| `--status` | no       | `active` (the default) or `archived`.                |
| `--limit`  | no       | Reviews per page. Defaults to 20, up to 100.         |
| `--page`   | no       | Page number, starting at 1.                          |

### `aviator verify`

Creates a review seeded with your acceptance criteria. The implementation stays with you — Aviator verifies the PR opened from the working branch against the criteria.

```bash
aviator verify \
  --repo myorg/myrepo \
  --intent "Add rate limiting to the public API so one client can't exhaust capacity" \
  --working-branch add-rate-limiting \
  --criteria-file criteria.txt
```

| Flag               | Required | Description                                                                                                                                                         |
| ------------------ | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `--repo`           | yes      | GitHub repo as `owner/repo`.                                                                                                                                        |
| `--intent`         | yes      | Short, plain-language description of what the change is for.                                                                                                        |
| `--criteria`       | one of   | A single acceptance criterion. Repeatable.                                                                                                                          |
| `--criteria-file`  | one of   | Path to a file with one criterion per line. Preferred for more than two or three criteria — it avoids shell-quoting problems. Mutually exclusive with `--criteria`. |
| `--working-branch` | no       | The branch the work lives on, so a PR opened from it is verified against these criteria.                                                                            |
| `--target-branch`  | no       | Base branch to verify against. Defaults to the repo default.                                                                                                        |
| `--spec`           | no       | Path to a spec file carrying the key decisions and architecture.                                                                                                    |

The command prints the review URL and the number of criteria it recorded. The first verification run happens when the PR is marked ready for review, or when you start one with `aviator verify r/123`.

A review tracks exactly one PR. Running `aviator verify` again for a branch that already has a review creates a second review. A PR opened from that branch then links to neither, unless its body links to one of them. Check with [`aviator sessions`](#aviator-sessions) first, and use [`aviator edit`](#aviator-edit) to change a review you already have. See [How a PR gets its review](github-integration.md#how-a-pr-gets-its-review).

For stacked PRs, submit once per PR, each with its own `--working-branch`, intent, and criteria, and pass the parent branch as `--target-branch`.

To start a verification run on an existing review, pass its ID: `aviator verify r/123`. If an equivalent run already exists for the current commit and criteria, you get that run back instead of a new one. `--force` starts a fresh full run anyway. `--evaluator-only` re-judges the evidence an earlier run collected instead of collecting it again. It's refused once criteria have been added or reworded, since those need a full run. The command prints the run's ID, and `aviator results r/123 --run <id>` follows that run even after a newer one starts.

### `aviator edit`

Updates the intent of an existing review, replaces its acceptance criteria, or both.

```bash
aviator edit r/123 --intent "Rate limit the public API per client"
aviator edit r/123 --expected-version 4 --criteria-file criteria.txt
```

Replacing criteria replaces the whole list, so pass the complete new set. `--expected-version` is required with criteria. Read the current version from `aviator show r/123`. If the criteria changed since you read it, the edit is refused and nothing is written, so read the version again and retry. Edits don't start a verification run; run `aviator verify r/123` when you're ready.

A criterion whose text is unchanged keeps its key and the way it's checked. A reworded criterion counts as new. It gets a new key, and the next full run plans how to check it. See [Understanding verification results](../how-to-guides/understanding-verification-results.md#the-criterion-is-wrong).

### `aviator dismiss`

Clears acceptance criteria off a review so they no longer gate the PR. A task criterion is deleted. An invariant is waived for this PR, with a category and a justification. `aviator show` and `aviator results` print each criterion's handle as `[key ...]` or `[invariant ...]`.

```bash
aviator dismiss r/123 --key 3f2a9c...
aviator dismiss r/123 --criteria-json '[
  {"baseline_invariant_id": 42, "category": "accepted_risk", "justification": "Known flake, tracked separately"}
]'
```

`--criteria-json` (or `--criteria-json-file`) takes a JSON array. Each entry is either `{"stable_key": "..."}` to delete a criterion, or `{"baseline_invariant_id": 42, "category": "...", "justification": "..."}` to waive an invariant. The categories are `false_positive`, `doesnt_apply`, `accepted_risk`, and `fix_in_followup`; see [Understanding verification results](../how-to-guides/understanding-verification-results.md#the-invariant-doesnt-fit-this-change) for when to use each. `--key` is shorthand for a `stable_key` entry and is repeatable.

`dismiss` needs a user access token, from `aviator login` or a personal token. An account-scoped API token is refused.

### `aviator scenarios` and `aviator evidence`

`aviator scenarios r/123` shows what the review's latest verification run exercised: each scenario's status, the criteria it covers, and the evidence it captured, with an ID for each piece of evidence. Every scenario that ran records a trace of the verifier's actions. Pass `--run <id>` to see an earlier run instead. `aviator show r/123` lists a review's recent runs with their IDs.

```bash
aviator scenarios r/123
aviator scenarios r/123 --run 887
aviator evidence 4567 -o trace.json
```

`aviator evidence <id>` prints a short-lived signed URL for the file. With `-o <path>` it downloads the file instead, or writes it to stdout with `-o -`. Like `curl -o`, it overwrites an existing file at that path once the download starts.

With `--json`, `aviator scenarios` also lists each scenario's steps. Each piece of evidence carries the `step_id` it was captured on. See [Understanding verification results](../how-to-guides/understanding-verification-results.md#reading-a-scenario-trace) for the trace format.

### `aviator decisions`

Reads and records a review's [decisions](../concepts/decisions.md). Requires v0.0.29 or later. The command is experimental and may change, so `aviator --help` doesn't list it yet. `aviator decisions --help` works, and `aviator decisions apply --help` prints the current batch format.

| Subcommand | What it does |
| ---------------------------------------------- | ------------------------------------------------------------------- |
| `list <review>` | List a review's decisions, each with its comment count. |
| `show <review> <decision-id>` | Show one decision with its comment thread. |
| `apply <review> --file <path>` | Create, change, settle, reopen, and comment from a JSON batch. `--file -` reads stdin. |

```bash
aviator decisions list r/123 --status open --json
aviator decisions show r/123 7 --json
aviator decisions apply r/123 --file batch.json --json
```

`list` flags:

| Flag       | Description                                |
| ---------- | ------------------------------------------ |
| `--status` | `open` or `settled`. Defaults to both.     |
| `--limit`  | Decisions per page. Defaults to 20, up to 100. |
| `--page`   | Page number, starting at 1.                |

#### Batch format

A batch is `{"decisions": [...]}` with at least one item, and it's all or nothing.

| Key | Applies to | Description |
| ------------ | ---------------- | --------------------------------------------------------------------------------- |
| `id` | Existing decision | The decision to change. Leave it out to create a decision. |
| `author` | Create or change | `{"role": ..., "model": ..., "harness": ..., "skills": [...]}`. `role` is required: `person`, `coding_agent`, or `review_agent`. |
| `type` | Create or change | `question`, `possible_bug`, `risk`, or `choice`. Required to create. |
| `title` | Create or change | Required to create. |
| `summary` | Create or change | Null clears it. |
| `anchor` | Create or change | `{"commit_sha", "file_path", "start_line", "end_line"}`, replaced as a whole. Null clears it. |
| `outcome` | Create or change | `decided`, `fixed`, `intended`, `accepted_risk`, `follow_up`, or `withdrawn`. Settles the decision and needs a `resolution`. Null reopens it and needs a new comment. |
| `resolution` | Create or change | Why it was settled this way. |
| `comments` | Any item | New comments, or edits to existing ones. Applied after the decision's change. |

An item with an `id` changes only the keys it has.

Each entry in `comments` is one of:

* **A new comment:** `{"author": {...}, "body": "...", "model_metadata": {...}}`. Without an `author` it takes the item's. `model_metadata` is optional.
* **An edit:** `{"id": 23, "body": "..."}`, with no other keys.

The rules the server enforces:

* Anyone can settle, reopen, and comment.
* Only the user who raised a decision can change its type, title, summary, or anchor.
* A settled decision has to be reopened before it can change.
* Only the user who wrote a comment can edit it.

`apply` needs a user access token, from `aviator login` or a personal token. An account-scoped API token can run `list` and `show`, but `apply` is refused.

The response has each item's decision in request order, with its comments. In `list` and `show` output, `raised_by` is the account that raised the decision, and `author` is the role and agent details of whoever wrote its current version. For a settled decision, that's who settled it. `author` here means who wrote the entry, not the PR's author.

See [Work with decisions](../how-to-guides/working-with-decisions.md#batch-recipes) for examples and errors.

### `aviator runbook`

Creates a runbook from an intent and hands the implementation to Aviator's agent. Acceptance criteria are optional here.

```bash
aviator runbook \
  --repo myorg/myrepo \
  --intent "Migrate the reporting jobs off the deprecated scheduler"
```

`--repo` and `--intent` are required. `--title`, `--target-branch`, `--spec`, `--criteria`/`--criteria-file`, and `--author-email` are optional, and `--oneshot` (on by default) controls one-shot mode.

### `aviator invariants`

Manages your account's [invariants](../concepts/invariants.md). `list`, `categories`, `insights`, `show`, `reviews`, and `waivers` work with any API token. The other subcommands need a user signed in with `aviator login` or a personal access token: anyone can `create`, and the rest need a maintainer, an admin, or a member of the invariant's [owner team](../concepts/invariants.md#owner-teams). Every subcommand takes `--json` to print the result as JSON.

| Subcommand                    | What it does                                                                                    |
| ----------------------------- | ----------------------------------------------------------------------------------------------- |
| `list`                        | List invariants, newest first.                                                                  |
| `categories`                  | List the category slugs that `create` and `edit` accept.                                       |
| `create`                      | Create an invariant. It's active immediately unless you pass `--disable`.                       |
| `edit <id>`                   | Change only the fields you pass.                                                                |
| `approve <id>...`             | Set invariants to `active`.                                                                     |
| `reject <id>...`              | Set invariants to `rejected`. They stop applying but stay on record.                            |
| `set-status <status> <id>...` | Set invariants to `pending`, `active`, or `rejected`.                                           |
| `delete <id>`                 | Delete an invariant permanently.                                                                |
| `list --usage`                | Add applied, failed, failure rate, and waived counts to each row.                                |
| `insights`                    | Rank active invariants by times applied, failure rate, and waivers, and list the never-applied ones. |
| `show <id>`                   | One invariant with its usage, by version and optionally by repository, and its latest waivers.  |
| `reviews <id>`                | The reviews an invariant applied to, with the outcome on each.                                   |
| `waivers`                     | Waivers across the account, newest first.                                                        |

`list` flags:

| Flag       | Description                                                                                                                  |
| ---------- | ---------------------------------------------------------------------------------------------------------------------------- |
| `--status` | `pending`, `active`, or `rejected`.                                                                                          |
| `--repo`   | Only invariants that apply to this `owner/repo`, including account-wide ones.                                                |
| `--source` | Comma-separated sources: `manual`, `ai_generated`, `ai_generated_docs`, `ai_generated_pr_comments`, `template`, `slack`, `github_comment`. |
| `--ids`    | Comma-separated invariant IDs.                                                                                               |
| `--limit`  | Invariants per page. Defaults to 20, up to 100.                                                                              |
| `--page`   | Page number, starting at 1. The JSON response's `has_more` says whether another page exists.                                 |
| `--usage`  | Add usage counts to each row, and show only active invariants unless `--status` is passed.                                   |
| `--sort`   | With `--usage`: `applied`, `failure_rate`, `waived`, or `waive_rate`, highest first.                                         |
| `--never-applied` | With `--usage`: only enabled invariants no review has applied.                                                        |

`create` and `edit` flags:

| Flag                        | Description                                                                                                                  |
| --------------------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| `--title`                   | Short name for the rule. Required on `create`.                                                                               |
| `--category`                | Category slug from `aviator invariants categories`. Required on `create`.                                                   |
| `--body`, `--body-file`     | The rule itself, inline or from a file. One is required on `create`.                                                         |
| `--repo`                    | Limit the invariant to this `owner/repo`. Repeatable. On `edit`, replaces the existing set.                                  |
| `--condition`               | `file_path_glob=<glob>` or `language=<tag>` to include, `file_path_glob!=<glob>` to exclude. Repeatable. On `edit`, replaces the existing set. See [Invariant conditions](invariant-conditions.md). |
| `--disable`                 | Turn the invariant off. On `edit`, only works on active invariants.                                                          |
| `--enable`                  | Turn an active invariant back on. Use `approve` for a pending one. `edit` only.                                             |
| `--account-scoped`          | Apply the invariant to every repository. `edit` only.                                                                        |
| `--no-conditions`           | Remove all conditions. `edit` only.                                                                                          |

`insights`, `show`, `reviews`, and `waivers` flags:

| Flag            | Description                                                                                                                  |
| --------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| `--since`       | `insights` only. Count only reviews since `<N>d` ago (for example `7d`), a `YYYY-MM-DD` date, or `all`. The last 30 days by default. |
| `--repo`        | Only reviews in this `owner/repo`. `insights`, `reviews`, and `waivers`.                                                     |
| `--status`      | `insights` only. Which invariants to rank. `active` by default.                                                              |
| `--limit`       | Rows per table on `insights`, rows per page on `reviews` and `waivers`. Defaults to 20, up to 100.                           |
| `--page`        | Page number, starting at 1. `reviews` and `waivers`.                                                                         |
| `--by-repo`     | `show` only. Add a per-repository breakdown.                                                                                 |
| `--outcome`     | `reviews` only. `pass`, `fail`, `warn`, `error`, `waived`, `fixed` (failed on an earlier run, passes now), or `not_run`. |
| `--invariant`   | `waivers` only. Only waivers of this invariant ID.                                                                           |
| `--user`        | `waivers` only. `me`, or an email on the account. `me` needs a user token.                                                   |
| `--category`    | `waivers` only. `false_positive`, `doesnt_apply`, `accepted_risk`, or `fix_in_followup`.                                     |

See [Managing invariants with the CLI](../how-to-guides/managing-invariants-with-the-cli.md) for what makes a good invariant.

### Using the CLI from a coding agent

You rarely type `aviator verify` by hand. The `/verify-submit` skill, from the [Aviator agent plugins](https://github.com/aviator-co/agent-plugins), reads the change, drafts the intent and acceptance criteria with you, and calls the CLI for you.

Run `aviator init` once per repo to have your agent remind you before a PR is opened. See [Set up agent hooks](../how-to-guides/set-up-agent-hooks.md).

The `/aviator-decide` skill, from the same plugin, records the decisions from your session with `aviator decisions apply`. See [Record decisions with aviator-decide](../how-to-guides/recording-decisions.md).

### See also

* [Set up agent hooks](../how-to-guides/set-up-agent-hooks.md) — the pre-PR reminder
* [Your first verification](../your-first-spec.md) — hands-on tutorial
* [Work with decisions](../how-to-guides/working-with-decisions.md) — read, answer, and record decisions
* [Writing effective acceptance criteria](../how-to-guides/writing-effective-acceptance-criteria.md)
