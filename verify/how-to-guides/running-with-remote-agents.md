# Running with remote agents

The default Verify flow is local: you work with your coding agent in your editor, and the [Aviator MCP](../reference/mcp-tools.md) submits the intent when you're done. This guide covers the alternative — running the agent remotely through Aviator Runbooks.

Use this when:

* You want batch or off-hours work — kick off ten tasks at once and come back to a stack of verified PRs.
* The change is driven by someone who isn't a developer — a PM filing a ticket-style task, a security team asking for a small fix across many repos.
* You want full attribution and reproducibility — the agent runs in a sandbox Aviator manages, with the same configuration every time.

This is a secondary flow. Most changes go through the local-MCP path. If you're picking your first integration, start there.

### How the remote flow differs

In the local flow:

```
You + agent (local) → Aviator MCP → Verify → Review document
```

In the remote flow:

```
Runbook (with task description) → Aviator-hosted agent (sandbox) → MCP → Verify → Review document
```

The implementation step moves into a sandbox. The MCP, verification, and review document are identical. The only thing that changes is *where* the agent runs and *who* drives it.

### Setting it up

You need:

* A Verify-connected repo, with at least one preview defined.
* A Runbook for the task — see [Runbooks: Getting started](../../runbooks/getting-started.md) for the basics.
* The same MCP install as the local flow, scoped to the Aviator-hosted agent.

The MCP is preconfigured for Aviator-hosted agents — you don't install it separately. Submissions are attributed to the user who triggered the runbook, not to the agent.

### Writing a runbook for Verify

For runbook authoring conventions — task description, scope, personas, sandboxes — see [Runbooks: Getting started](../../runbooks/getting-started.md) and [Runbooks: Runbook Format](../../runbooks/concepts/runbook-format.md).

The Verify-specific addition: write a task description specific enough that the remote agent can implement against it without follow-up clarification. Vague descriptions produce runbooks that drift from the intent — and intent drift is exactly what verification is supposed to catch, so this gets noisy fast.

When the runbook completes, verification runs against the resulting branch the same way it does for any other submission. The review document is identical.

### What you trade off

Compared to the local flow:

| Local flow                                    | Remote (Runbook) flow                                |
| --------------------------------------------- | ---------------------------------------------------- |
| Fast iteration, immediate context             | Higher latency per task                              |
| You see and steer the agent's choices live    | The runbook description must be precise upfront      |
| Editor + branch state is your environment     | A clean sandbox per run                              |
| Submits attributed to the implementing user   | Submits attributed to whoever triggered the runbook  |
| One task at a time                            | Batch many tasks                                     |

The remote flow is better for *replication*. The local flow is better for *exploration*.

### Reviewing the result

The review document is identical to the local flow — same intent + criteria + verdicts + evidence surface. Reviewers can tell a remote-flow change apart by the submission attribution (it shows the Runbook ID alongside the user), but they don't have to treat it differently.

If a remote run fails verification, two recovery paths:

* **Re-run the runbook.** If the failure was transient or the description needed sharpening, re-running is the cheapest fix.
* **Take it local.** Check out the branch, open it in your local agent, iterate, and submit through the local MCP. The audit trail records both submissions.

### See also

* [How Verify works](../how-it-works.md)
* [MCP tools](../reference/mcp-tools.md)
* [Runbooks: Getting started](../../runbooks/getting-started.md) — for the runbook side of the flow
