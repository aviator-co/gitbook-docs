# Your first verification

In this tutorial, you'll ship a small change end to end through Verify: implement it with your agent, submit the intent through the Aviator MCP, watch verification run, and read the review document.

By the end, you'll have a working loop and a sense of where each piece fits.

**Time:** ~15 minutes

**Prerequisites:**

* An Aviator org with a connected GitHub repo — see [Connect a repository](how-to-guides/connect-a-repository.md)
* A coding agent that supports MCP — Claude Code, Cursor, or another MCP-compatible client
* Write access to the repo

You **don't need a preview** to do this tutorial. Without one, Verify runs code-scan only — every criterion is checked against the diff. You'll see a fully working loop end to end. Add a preview later when you want behavioral (runtime) verdicts; see [Creating a preview](how-to-guides/creating-a-preview.md).

### Step 1: Install the Aviator MCP in your agent

The MCP is how the agent talks to Aviator. Install it once per agent.

1. In the Aviator UI, go to **Settings → Integrations → MCP** and copy your install snippet (it includes a scoped token).
2. Add the snippet to your agent's MCP server configuration. See your client's MCP documentation for where this lives.
3. Restart the agent. You should see the Aviator tools listed in the agent's available tools.

A successful install gives your agent access to three Aviator tools: `specSubmit`, `getRunbook`, and `editRunbook`. You only need `specSubmit` for this tutorial. See [MCP tools](reference/mcp-tools.md) for the full surface.

### Step 2: Pick a small change

For the tutorial, pick something small and concrete. Examples that work well for a first run:

* Add a `GET /health` endpoint that returns `{"status": "ok"}` with a 200.
* Add a `/version` endpoint that returns the current commit SHA.
* Add a feature flag check to an existing endpoint.

Keep the scope to one or two files. You're learning the loop, not stress-testing the verifier.

### Step 3: Implement the change

Open your agent in the repo. Describe the change normally:

> Add a `GET /health` endpoint to the API. It should return `{"status": "ok"}` with a 200 response and not require authentication.

Let the agent implement it. Iterate until you're aligned on the details — same flow you use today. Don't worry about Verify yet.

When you're happy with the change, tell the agent it's ready.

### Step 4: Submit the intent

Tell the agent to submit the intent:

> Submit this to Aviator Verify.

The agent calls `specSubmit` through the MCP. The tool creates a runbook carrying:

* **Intent** — what the change is for, captured from your conversation.
* **Acceptance criteria** — the verifiable assertions, generated from what was built.

The MCP returns a runbook URL like `https://app.aviator.co/r/218`. The agent will print it.

Open the URL in your browser. You'll see the runbook with the generated plan, the acceptance criteria, and (once verification starts) a streaming verdict per criterion.

### Step 5: Watch verification run

The runbook page updates as the verification pipeline progresses. The phases:

1. **Runbook generation.** Aviator turns the submission into a structured plan + acceptance criteria. Any matching invariants from your account catalog are materialized as additional criteria.
2. **Preview is built and booted** (only if you've configured a preview). Your image is loaded, secrets injected, setup script runs.
3. **Criteria run.** Each criterion is routed to one of two verifier paths — code-scan (static analysis of the diff) or runtime (executed against the preview). Without a preview, every criterion routes to code-scan.

For a small change, the whole run completes in 30–90 seconds.

### Step 6: Read the review document

When the run finishes, each criterion shows:

* **Verdict** — `pass`, `fail`, `warn`, or `error`.
* **Verifier** — which path ran it (Code-scan or Runtime).
* **Evidence** — the diff snippet (code-scan) or runtime output (request/response, screenshot, log line) that backs the verdict.

For the health endpoint without a preview, you should see something like:

| Criterion                            | Verifier  | Verdict |
| ------------------------------------ | --------- | ------- |
| Endpoint `GET /health` exists        | Code-scan | ✓ Pass  |
| Returns 200                          | Code-scan | ✓ Pass  |
| Response body is `{"status": "ok"}`  | Code-scan | ✓ Pass  |
| Does not require authentication      | Code-scan | ✓ Pass  |

With a preview configured, the behavioral criteria would route to Runtime instead — and you'd see the actual request + response as evidence.

Click any verdict to see the evidence. If something failed, the verdict is annotated with the file and line that caused it. Fix the issue, then start another run from the runbook UI — a plain push doesn't re-run Verify on its own (see [When a run is triggered](concepts/how-verification-works.md#when-a-run-is-triggered)).

### Step 7: Approve (or send back)

From the review document you can:

* **Approve** — sign off and continue your normal merge flow.
* **Waive a failed verdict with a category** — `false_positive`, `doesnt_apply`, `accepted_risk`, or `fix_in_followup`. Recorded in the audit trail.
* **Edit the acceptance criteria** — ask the agent to call `editRunbook` with the corrected criteria, then re-verify.
* **Open the preview** — if you have one configured, poke at the running code yourself before approving.

Approve to close the loop. The audit trail now has a complete record: runbook submission, verdicts per criterion with evidence, your decision.

### What you just learned

* Verify runs *after* you build, not before. There's no separate "approve the spec first" step.
* The MCP is the only handoff between you and Aviator. One tool call carries everything.
* Acceptance criteria come from what was built, not what you planned upfront.
* The review document is the surface — verdicts and evidence per criterion, not a 500-line diff.
* Reviewer decisions, scenario evidence, and verdicts are all recorded as one immutable audit trail per change.

### Next steps

* [Creating a preview](how-to-guides/creating-a-preview.md) — unlock runtime verdicts. This is usually the next step once you have a feel for the loop.
* [How Verify works](how-it-works.md) — the full picture
* [Concepts: Invariants](concepts/invariants.md) — encode team rules so they apply automatically
* [Writing a SKILL.md](how-to-guides/writing-a-skill-md.md) — give the scenario runner the context it needs
* [Fixing verification failures](how-to-guides/fixing-verification-failures.md) — what to do when a verdict goes red
