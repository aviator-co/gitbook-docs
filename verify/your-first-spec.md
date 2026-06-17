# Your first verification

In this tutorial, you'll ship a small change end to end through Verify: implement it with your agent, submit the intent through the Aviator MCP, watch verification run, and read the review document.

By the end, you'll have a working loop and a sense of where each piece fits.

**Time:** ~15 minutes

**Prerequisites:**

* An Aviator org with a connected GitHub repo — see [Connect a repository](how-to-guides/connect-a-repository.md)
* A coding agent that supports MCP — Claude Code, Cursor, or another MCP-compatible client
* Write access to the repo
* A preview configured for the repo — see [Creating a preview](how-to-guides/creating-a-preview.md). For a first run, a minimal one is fine.

### Step 1: Install the Aviator MCP in your agent

The MCP is how the agent talks to Aviator. Install it once per agent.

1. In the Aviator UI, go to **Settings → Integrations → MCP** and copy your install snippet (it includes a scoped token).
2. Paste it into your agent's MCP config:
   * **Claude Code:** `~/.claude/mcp_servers.json`
   * **Cursor:** Cursor → Settings → MCP → Add server
   * Other clients: see your client's MCP documentation.
3. Restart the agent. You should see `aviator` listed in the available MCP tools.

A successful install gives your agent access to one tool: `submit-spec`. That's the only one you need today.

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

The agent calls `submit-spec` through the MCP. The tool captures:

* **Intent** — what the change is for, in plain language.
* **Acceptance criteria** — the verifiable assertions, generated from what was built.

The MCP returns a review URL like `https://app.aviator.co/r/218/review`. The agent will print it.

Open the URL in your browser. You'll land on the review document — three regions: intent at the top, evidence in the middle, decisions at the bottom. Right now it shows "Verifying…" while the run executes.

### Step 5: Watch verification run

The review document updates as the verification pipeline progresses. The phases:

1. **Preview is built and booted.** Your image is pulled, secrets injected, setup script runs. Logged inline.
2. **Criteria run in parallel.** Each acceptance criterion is routed to a verifier (code-scan, scenario, invariant, or LLM fallback) and produces a verdict + evidence.
3. **Invariants are checked.** Any org or repo invariants matching the change run alongside.

For a small change, the whole run completes in 30–90 seconds.

### Step 6: Read the review document

When the run finishes, each criterion shows:

* **Verdict** — pass, fail, or waived.
* **Verifier** — which method ran it (Scenario, Code-scan, Invariant, LLM).
* **Evidence** — the diff snippet, scenario output, or matched rule that backs the verdict.

For the health endpoint, you should see something like:

| Criterion                            | Verifier  | Verdict |
| ------------------------------------ | --------- | ------- |
| Endpoint `GET /health` exists        | Code-scan | ✓ Pass  |
| Returns 200                          | Scenario  | ✓ Pass  |
| Response body is `{"status": "ok"}`  | Scenario  | ✓ Pass  |
| Does not require authentication      | Scenario  | ✓ Pass  |

Click any verdict to see the evidence. For scenarios, the evidence is the actual request + response from the preview.

If something failed, the verdict is annotated with the file and line that caused it. Push a fix and Verify will re-run automatically — no new submission needed.

### Step 7: Approve (or send back)

From the review document you can:

* **Approve** — sign off and continue your normal merge flow.
* **Waive with reason** — accept a failed criterion explicitly (recorded in the audit trail).
* **Request another scenario** — ask the agent to test a specific case before you decide.
* **Open the preview** — poke at the running code yourself before approving.

Approve to close the loop. The audit trail now has a complete record: intent submitted, verdicts per criterion with evidence, your decision.

### What you just learned

* Verify runs *after* you build, not before. There's no separate "approve the spec first" step.
* The MCP is the only handoff between you and Aviator. One tool call carries everything.
* Acceptance criteria come from what was built, not what you planned upfront.
* The review document is the surface — verdicts and evidence per criterion, not a 500-line diff.
* Reviewer decisions, scenario evidence, and verdicts are all recorded as one immutable audit trail per change.

### Next steps

* [How Verify works](how-it-works.md) — the full picture
* [Concepts: Previews](concepts/previews.md) — what scenarios actually run against
* [Concepts: Invariants](concepts/invariants.md) — encode team rules so they apply automatically
* [Writing a SKILL.md](how-to-guides/writing-a-skill-md.md) — give the scenario runner the context it needs
* [Fixing verification failures](how-to-guides/fixing-verification-failures.md) — what to do when a verdict goes red
