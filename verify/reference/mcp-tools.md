# MCP tools

The Aviator MCP server is how your coding agent talks to Verify. It exposes three tools for creating, reading, and editing the runbook that drives a verification run.

This page documents the tool surface. For the conceptual flow, see [How Verify works](../how-it-works.md).

### Installing the MCP

The install snippet — including a scoped token — is available in the Aviator UI under **Settings → Integrations → MCP**. Configure your agent to load the Aviator MCP server using the snippet for your client.

Restart the agent after configuring. The Aviator tools should appear in the agent's available-tools list.

### Tools

| Tool          | Use it for                                                              |
| ------------- | ----------------------------------------------------------------------- |
| `specSubmit`  | Submit a new runbook capturing the intent and acceptance criteria.      |
| `getRunbook`  | Read the current state of a runbook — plan, criteria, latest verdict.   |
| `editRunbook` | Replace acceptance criteria on an existing runbook in a versioned edit. |

### `specSubmit`

Submits a runbook to Aviator. It captures two things: the **intent** for the change (what it's for, in plain language), and the **acceptance criteria** the change must satisfy. From this point on, verification runs against this runbook.

**When to call it:** when the user explicitly asks to submit to Aviator. Never call it proactively.

**Parameters:**

| Parameter       | Required | Description                                                                                            |
| --------------- | -------- | ------------------------------------------------------------------------------------------------------ |
| `repo_name`     | yes      | GitHub repo name. Accepts `owner/repo` or just `repo` if unambiguous within the account.               |
| `message`       | yes      | The intent — a short, plain-language description of what this change is for and why.                   |
| `working_branch`| no       | An existing branch where the work already lives. Setting this lets the runbook read what's already pushed and auto-connect a future PR. Omit to have the runbook author the work from scratch. |
| `target_branch` | no       | The branch the work targets. Omit for the repo default (trunk); pass the parent branch when this work stacks on another in-flight branch. |

**Returns:**

```json
{
  "runbook_number": "218",
  "url": "https://app.aviator.co/r/218",
  "message": "Runbook creation started. Generation may take a few minutes."
}
```

Open the URL to watch the runbook generate, see acceptance criteria, and read verdicts as verification runs.

### `getRunbook`

Reads the current state of a runbook. Use it before editing — every edit takes an `expected_version` you read from here.

**Parameters:**

| Parameter | Required | Description                                                                  |
| --------- | -------- | ---------------------------------------------------------------------------- |
| `url`     | yes      | The runbook URL. Accepts `https://app.aviator.co/r/{number}`, `runbooks/{id}`, or plain `r/{number}`. |
| `fields`  | no       | List of fields to return. Defaults to all. Selectable: `steps_markdown`, `runbook_state`, `acceptance_criteria`. `runbook_version` is always returned. |

**Returns (per field):**

| Field                   | Shape                                                                                  |
| ----------------------- | -------------------------------------------------------------------------------------- |
| `runbook_version`       | int — always present. Pass back as `expected_version` on edits.                        |
| `steps_markdown`        | The runbook plan as markdown.                                                          |
| `runbook_state`         | `target_branch`, `working_branch`, per-step status.                                    |
| `acceptance_criteria`   | List of `{ordinal, raw_text, source}`. Baseline-invariant criteria are excluded — they're auto-managed. |
| `latest_verification`   | Returned alongside `acceptance_criteria`. Null if no runs. Includes status, criteria counts, and a list of non-passing results (each tagged with `is_invariant` and `is_waived`). |

### `editRunbook`

Edits a runbook artifact. Today only `acceptance_criteria` is editable. Baseline invariants are auto-managed and never included in the payload.

**Parameters:**

| Parameter          | Required | Description                                                                            |
| ------------------ | -------- | -------------------------------------------------------------------------------------- |
| `runbook_url`      | yes      | The runbook URL (same formats as `getRunbook`).                                        |
| `expected_version` | yes      | The `runbook_version` you read with `getRunbook`. If the runbook has changed since, the call fails with a stale-version error — re-read and retry. |
| `payload`          | yes      | JSON object keyed by artifact name. Each value is the COMPLETE new state of that artifact. For `acceptance_criteria`: a JSON array of strings (the new criterion texts in order). One call expresses add/update/remove/reorder in a single atomic edit. |

**Example payload:**

```json
{
  "acceptance_criteria": [
    "First criterion text",
    "Second criterion text",
    "Third criterion text"
  ]
}
```

**Returns:**

```json
{
  "results": {"acceptance_criteria": {...}},
  "new_version": 5,
  "message": "Runbook updated to version 5."
}
```

### Authorization

Each MCP token is scoped to the user who generated it:

* Submissions are attributed to that user in the audit trail.
* Token permissions are bounded by the user's account role — a viewer-only user can't create runbooks for a repo they can't write to.
* Tokens can be rotated from the Aviator UI; rotation invalidates the previous token immediately.

If a teammate's submission needs to be attributed to them, they need their own install — sharing tokens defeats the audit trail.

### Versioning

The MCP tool surface follows backwards-compatibility rules:

* Field additions are backwards-compatible. New optional parameters may appear without notice.
* Field removals and required-field changes are versioned. The install snippet pins to a major version; breaking changes ship as a new major.
* Token format is stable across versions.

### See also

* [How Verify works](../how-it-works.md) — where the MCP fits in the loop
* [Your first verification](../your-first-spec.md) — hands-on tutorial
* [Running with remote agents](../how-to-guides/running-with-remote-agents.md) — alternative flow that uses Runbooks directly
