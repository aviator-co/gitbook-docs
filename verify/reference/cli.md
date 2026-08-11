# Aviator CLI

The `aviator` CLI submits intent and acceptance criteria to Verify from your terminal or from inside a coding agent. It is the preferred way to talk to Verify.

This is a different tool from `av`, the [Stacked PRs CLI](../../aviator-cli/README.md). The two are installed separately and don't depend on each other.

### Install

```bash
brew install aviator-co/tap/aviator
```

Linux users can install the `.deb` or `.rpm` package from the [releases page](https://github.com/aviator-co/aviator-cli/releases), or download the archive for their platform.

Confirm the install:

```bash
aviator version
```

### Authentication

The CLI reads an API token from the `AVIATOR_API_TOKEN` environment variable:

```bash
export AVIATOR_API_TOKEN=<your token>
```

Create a User Access Token at [app.aviator.co/settings/personal/api_token](https://app.aviator.co/settings/personal/api_token). Submissions are attributed to the user the token belongs to, so each person needs their own — a shared token collapses the audit trail.

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

| Command           | What it does                                                                    |
| ----------------- | ------------------------------------------------------------------------------- |
| `aviator verify`  | Submit intent and acceptance criteria for a change you're writing yourself.     |
| `aviator runbook` | Create a runbook and have Aviator's agent implement the change.                 |
| `aviator show`    | Show a runbook or Verify session, e.g. `aviator show r/123`.                    |
| `aviator results` | Show the latest verification results for a session.                             |
| `aviator edit`    | Replace the acceptance criteria on an existing session. Takes `--expected-version` to guard against stale edits — read the current version with `aviator show`. |
| `aviator init`    | Set up your coding agents to capture intent before a PR. See [Set up agent hooks](../how-to-guides/set-up-agent-hooks.md). |
| `aviator hooks`   | Manage the hooks `init` installed — `aviator hooks uninstall` removes them.     |
| `aviator version` | Print the CLI version.                                                          |

### `aviator verify`

Creates a Verify session seeded with your acceptance criteria. The implementation stays with you — Aviator verifies the PR opened from the working branch against the criteria.

```bash
aviator verify \
  --repo myorg/myrepo \
  --intent "Add rate limiting to the public API so one client can't exhaust capacity" \
  --working-branch add-rate-limiting \
  --criteria-file criteria.txt
```

| Flag               | Required | Description                                                                     |
| ------------------ | -------- | --------------------------------------------------------------------------------- |
| `--repo`           | yes      | GitHub repo as `owner/repo`.                                                    |
| `--intent`         | yes      | Short, plain-language description of what the change is for.                    |
| `--criteria`       | one of   | A single acceptance criterion. Repeatable.                                      |
| `--criteria-file`  | one of   | Path to a file with one criterion per line. Preferred for more than two or three criteria — it avoids shell-quoting problems. Mutually exclusive with `--criteria`. |
| `--working-branch` | no       | The branch the work lives on, so a PR opened from it is verified against these criteria. |
| `--target-branch`  | no       | Base branch to verify against. Defaults to the repo default.                    |
| `--spec`           | no       | Path to a spec file carrying the key decisions and architecture.                |
| `--author-email`   | no       | Attribute the submission to a different user.                                   |

The command prints the session URL and the number of criteria it recorded. The first verification run happens when the PR is marked ready for review.

To change criteria on a session that already exists, use `aviator edit` — re-running `aviator verify` creates a new session.

### `aviator runbook`

Creates a runbook from an intent and hands the implementation to Aviator's agent. Acceptance criteria are optional here.

```bash
aviator runbook \
  --repo myorg/myrepo \
  --intent "Migrate the reporting jobs off the deprecated scheduler"
```

`--repo` and `--intent` are required. `--title`, `--target-branch`, `--spec`, `--criteria`/`--criteria-file`, and `--author-email` are optional, and `--oneshot` (on by default) controls one-shot mode.

### Using the CLI from a coding agent

You rarely type `aviator verify` by hand. The `/verify-submit` skill, from the [Aviator agent plugins](https://github.com/aviator-co/agent-plugins), reads the change, drafts the intent and acceptance criteria with you, and calls the CLI for you.

Run `aviator init` once per repo to have your agent remind you before a PR is opened. See [Set up agent hooks](../how-to-guides/set-up-agent-hooks.md).

### See also

* [Set up agent hooks](../how-to-guides/set-up-agent-hooks.md) — the pre-PR reminder
* [Your first verification](../your-first-spec.md) — hands-on tutorial
* [Writing effective acceptance criteria](../how-to-guides/writing-effective-acceptance-criteria.md)
* [MCP tools](mcp-tools.md) — the legacy submission path
