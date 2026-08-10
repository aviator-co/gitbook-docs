# Set up agent hooks

Intent is easiest to capture while the reasoning behind a change is still in the agent's context. Once the PR is open and the session has moved on, it has to be reconstructed from the diff — which is exactly the information Verify needs and the diff doesn't carry.

`aviator init` sets up your coding agents to capture that intent as you open a pull request. It installs two hooks: a standing instruction at the start of every session, and a reminder that fires when the agent is about to open a PR.

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

It asks who the setup is for and which agents to cover, then writes the hook configuration. Re-run it any time to add an agent or reconcile existing config — it's idempotent, and reports whether each hook was added, updated, or already in place.

#### Who the setup covers

This decides where the configuration lands.

| Set up for     | Where the hook is written                                                | Who it covers                                              |
| -------------- | -------------------------------------------------------------------------- | ---------------------------------------------------------- |
| The whole repo | `.claude/settings.json` or `.codex/hooks.json` in the repository         | Everyone who works on this repo, once you commit the files |
| Just you       | Your own agent config — `~/.claude/settings.json`, `~/.codex/hooks.json` | You, in every repository on this machine                    |

Set it up for the **whole repo** to roll Verify out to a team — committing the files is what shares it. Set it up for **just yourself** to get the reminder without adding anything to the repository; it covers every repo you touch on this machine. If you've set `CLAUDE_CONFIG_DIR` or `CODEX_HOME`, the CLI honors them.

#### Which agents to cover

Pick the agents your team actually uses. A repo-wide setup preselects all supported agents, since it can't detect what your teammates run; a personal setup preselects the ones it finds on this machine.

Everyone the setup covers needs the CLI installed, their own `AVIATOR_API_TOKEN`, and the `/verify-submit` skill.

### What the hooks do

`init` adds two hook entries per agent, each calling back into the CLI.

**`SessionStart`** runs `aviator hooks session-start`. It gives the agent a standing instruction: this repository uses Aviator Verify, capture the change's intent and acceptance criteria with `/verify-submit` before opening a pull request. This is the one doing the real work — session start is the only point that reaches the agent reliably ahead of a PR. If the skill isn't installed, the instruction also says how to get it for that agent.

**`PreToolUse`** runs `aviator hooks pre-tool-use`. It watches for PR-opening calls — `gh pr create`, `av pr`, and the GitHub MCP server's `create_pull_request` tool — and injects a reminder when it sees one. Treat it as a backstop rather than a gate: Claude and Codex deliver this context alongside the tool result, so the agent usually reads it just after the PR command has run. Every other tool call passes through untouched.

Both hooks only inject text. Neither submits anything and neither blocks a tool call. `/verify-submit` is what reads the change, drafts the intent and acceptance criteria with you, and calls [`aviator verify`](../reference/cli.md#aviator-verify).

### Codex needs one extra step

Codex won't fire a hook it hasn't been told to trust. After running `aviator init`, run `/hooks` inside Codex and trust the Aviator hook. Until you do, the configuration is in place but nothing fires.

### Non-interactive use

`aviator init` is meant to be run interactively — the prompts are how you make the scope decision. For provisioning scripts, machine images, and other places where nobody is there to answer, pass the answers as flags instead:

```bash
aviator init --scope team --agents claude,codex --yes
```

| Flag       | Description                                                                                         |
| ---------- | ----------------------------------------------------------------------------------------------------- |
| `--scope`  | `team` or `self`. Prompts if omitted.                                                               |
| `--agents` | Comma-separated agent ids (`claude`, `codex`). Defaults to all supported agents for team scope, detected agents for self. |
| `--yes`    | Skip the prompts. Requires `--scope`.                                                               |

Outside a terminal the command can't prompt, so `--scope` is required there.

### Removing the hooks

```bash
aviator hooks uninstall
```

This clears both scopes by default, since self-scope config lives outside the repo and is easy to forget. Narrow it with `--scope team` or `--scope self`.

Uninstall removes only Aviator's own hook entries. Other hooks, and every other key in the settings file, are left alone.

### See also

* [Aviator CLI](../reference/cli.md) — install, authentication, and the full command surface
* [Writing effective acceptance criteria](writing-effective-acceptance-criteria.md)
* [Your first verification](../your-first-spec.md)
