# Set up agent hooks

Intent is easiest to capture while the reasoning behind a change is still in the agent's context. Once the PR is open and the session has moved on, it has to be reconstructed from the diff — which is exactly the information Verify needs and the diff doesn't carry.

`aviator init` sets up your coding agents to capture that intent as you open a pull request. It installs three hooks: a standing instruction at the start of every session, a reminder when the agent opens a PR, and a check-in after each commit or push.

### Prerequisites

* The [Aviator CLI](../reference/cli.md)
* An API token, as `AVIATOR_API_TOKEN` or in `~/.config/aviator/config.yaml` — see [Authentication](../reference/cli.md#authentication)
* The `/verify-submit` skill from the [Aviator agent plugins](https://github.com/aviator-co/agent-plugins)
* A supported agent — Claude Code or Codex

### Run init

From inside the repository:

```bash
aviator init
```

By default it sets up the whole repo. It asks which agents to cover, then writes the hook configuration. Re-run it any time to add an agent or reconcile existing config — it's idempotent, and reports whether each hook was added, updated, or already in place.

#### Who the setup covers

`--scope` decides where the configuration lands.

| `--scope`        | Where the hook is written                                                  | Who it covers                                              |
| ---------------- | -------------------------------------------------------------------------- | ---------------------------------------------------------- |
| `team` (default) | `.claude/settings.json` or `.codex/hooks.json` in the repository           | Everyone who works on this repo, once you commit the files |
| `self`           | Your own agent config: `~/.claude/settings.json`, `~/.codex/hooks.json`    | You, in every repository on this machine                   |
| `local`          | `.claude/settings.local.json` in the repository. Claude Code only.         | You, in this repository only                               |

Use `team` to roll Verify out to a team. Committing the files is what shares it. Use `self` to get the hooks without adding anything to any repository, in every repo you work in on this machine. Use `local` to try Verify in one repo without committing anything. Don't commit `.claude/settings.local.json`. Codex has no per-user repo config, so `local` skips it. If you've set `CLAUDE_CONFIG_DIR` or `CODEX_HOME`, the CLI honors them.

#### Which agents to cover

Pick the agents your team actually uses. A `team` setup preselects all supported agents, since it can't detect what your teammates run. `self` and `local` preselect the ones it finds on this machine.

Everyone the setup covers needs the CLI installed, their own `AVIATOR_API_TOKEN`, and the `/verify-submit` skill.

### What the hooks do

`init` adds three hook entries per agent, each calling back into the CLI.

**`SessionStart`** runs `aviator hooks session-start`. It gives the agent a standing instruction: this repository uses Aviator Verify, capture the change's intent and acceptance criteria with `/verify-submit` before opening a pull request. This is the one doing the real work — session start is the only point that reaches the agent reliably ahead of a PR. If the skill isn't installed, the instruction also says how to get it for that agent.

**`PreToolUse`** runs `aviator hooks pre-tool-use`. It watches for PR-opening calls — `gh pr create`, `av pr`, and the GitHub MCP server's `create_pull_request` tool — and injects a reminder when it sees one. Treat it as a backstop rather than a gate: Claude and Codex deliver this context alongside the tool result, so the agent usually reads it just after the PR command has run. Every other tool call passes through untouched.

**`PostToolUse`** runs `aviator hooks post-tool-use`. After a shell command that commits or pushes, it reminds the agent to look up the branch's review with `aviator sessions` and keep its criteria in line with the code, or to run `/verify-submit` before opening a PR if there's no review yet.

The hooks only inject text. None of them submits anything or blocks a tool call. `/verify-submit` is what reads the change, drafts the intent and acceptance criteria with you, and calls [`aviator verify`](../reference/cli.md#aviator-verify).

### Codex needs one extra step

Codex won't fire a hook it hasn't been told to trust. After running `aviator init`, run `/hooks` inside Codex and trust the Aviator hook. Until you do, the configuration is in place but nothing fires.

### Non-interactive use

For provisioning scripts, machine images, and other places where nobody is there to answer the agent prompt, pass the answers as flags:

```bash
aviator init --scope team --agents claude,codex --yes
```

| Flag       | Description                                                                                         |
| ---------- | ----------------------------------------------------------------------------------------------------- |
| `--scope`  | `team` (the default), `self`, or `local`.                                                           |
| `--agents` | Comma-separated agent ids (`claude`, `codex`). Defaults to all supported agents for `team`, and the ones detected on this machine otherwise. |
| `--yes`    | Skip the prompts.                                                                                   |

### Removing the hooks

```bash
aviator hooks uninstall
```

This clears all three scopes by default, since `self` and `local` config are easy to forget. Narrow it with `--scope team`, `--scope self`, or `--scope local`.

Uninstall removes only Aviator's own hook entries. Other hooks, and every other key in the settings file, are left alone.

### See also

* [Aviator CLI](../reference/cli.md) — install, authentication, and the full command surface
* [Writing effective acceptance criteria](writing-effective-acceptance-criteria.md)
* [Your first verification](../your-first-spec.md)
