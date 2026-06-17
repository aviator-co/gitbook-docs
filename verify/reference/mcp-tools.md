# MCP tools

The Aviator MCP server is how your coding agent talks to Verify. It exposes one tool that the agent calls to submit an intent and trigger verification.

This page documents the tool surface. For the conceptual flow, see [How Verify works](../how-it-works.md).

### Installing the MCP

The install snippet — including a scoped token — is available in the Aviator UI:

**Settings → Integrations → MCP → Install**

The snippet is per-org and embeds a token tied to your user. Don't commit it to a repo; treat it like any other credential.

Configure your agent to load the MCP server:

| Agent           | Where to add it                                                                       |
| --------------- | ------------------------------------------------------------------------------------- |
| **Claude Code** | `~/.claude/mcp_servers.json` — add an `aviator` entry from the install snippet.       |
| **Cursor**      | Cursor → Settings → MCP → Add server.                                                 |
| **Other**       | Any MCP-compatible client. Follow the client's MCP configuration documentation.       |

Restart the agent after configuring. The tool should appear in the agent's available-tools list.

### `submit-spec`

This is the only tool you need for the end-to-end flow. Despite the name, the workflow it implements is "submit intent from local" — the tool captures both the intent and the acceptance criteria the agent generated from the implementation.

**When to call it:** after the agent finishes implementing a change and you're ready to hand off for verification.

**What it captures:**

| Field                | Source                                                  | Purpose                                              |
| -------------------- | ------------------------------------------------------- | ---------------------------------------------------- |
| Intent               | The conversation between you and the agent              | The *why* — constraints not visible from the diff.   |
| Acceptance criteria  | The agent's analysis of what was actually built         | Verifiable assertions, one per criterion.            |
| Repo and branch      | The git context the agent is operating in               | Identifies which preview to use, which invariants apply. |
| Change set           | The diff against the target branch                      | Scope for code-scan and invariant matching.          |

**What it returns:**

* A review URL — e.g. `https://app.aviator.co/r/218/review`. Open it to watch verification run and read the verdicts.
* A run identifier for log correlation.

The tool blocks briefly while the submission is accepted, then returns. Verification continues asynchronously.

### Authorization

Each MCP token is scoped to the user who generated it and inherits that user's permissions:

* Submissions are attributed to the user in the audit trail.
* Token permissions are bounded by the user's org role — a viewer-only user can't submit changes against a repo they can't write to.
* Tokens can be rotated from the Aviator UI; rotation invalidates the previous token immediately.

If a teammate's submission needs to be attributed to them, they need their own install — sharing tokens defeats the audit trail.

### When the MCP isn't installed

If the agent doesn't have the MCP loaded, the `submit-spec` tool won't appear in its tool list. You'll see the agent say something like "I don't have a tool to submit to Aviator."

Two recovery paths:

* **Install and retry.** Follow the install steps above and re-prompt the agent.
* **Submit through the Aviator UI.** Open the repo in the Aviator UI and use **New verification → Submit manually**. Paste the intent and criteria from the agent's transcript. Slower than the MCP path, but produces the same review document.

### Versioning

The MCP tool surface follows the same backwards-compatibility rules as the rest of Verify:

* Field additions are backwards-compatible. New optional fields may appear without notice.
* Field removals and required-field changes are versioned. The install snippet pins to a major version; breaking changes ship as a new major.
* Token format is stable across versions.

### See also

* [How Verify works](../how-it-works.md) — where the MCP fits in the pipeline
* [Your first verification](../your-first-spec.md) — hands-on tutorial
* [Running with remote agents](../how-to-guides/running-with-remote-agents.md) — alternative flow that uses Runbooks instead of a local MCP
