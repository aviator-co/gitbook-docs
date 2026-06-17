# Verification layers

A verification run combines two categories of checks: **acceptance criteria** that came from this change's intent, and **invariants** that the team defined ahead of time. They produce verdicts on the same review document and pass or fail independently.

This page is about how those two categories interact at run time. For the deeper concept of invariants themselves — scope, writing them well, exceptions — see [Invariants](invariants.md).

### The two categories

| Category                | Where it comes from                                        | Scope                                | When it's defined         |
| ----------------------- | ---------------------------------------------------------- | ------------------------------------ | ------------------------- |
| **Acceptance criteria** | The agent's MCP submission, generated from the intent      | This change only                     | Per change, every time    |
| **Invariants**          | Your org's invariant configuration                         | Every matching change                | Once; updated rarely      |

Acceptance criteria are what the change is *trying* to do. Invariants are what every change *must* respect. Both produce verdicts. Both block merge if they fail (unless waived).

### How they combine at run time

When verification starts, Aviator assembles the check set:

1. **Every acceptance criterion** from the submission runs. Each one is routed through the verifier pipeline ([How verification works](how-verification-works.md)).
2. **Every matching invariant** is added. An invariant matches if its scope (org / repo / path) intersects with the files the change touches.

The two sets are unioned. A typical run has 3–7 acceptance criteria and a handful of matching invariants. Both flow through the same verifier pipeline, both produce verdicts with evidence in the same review document.

A run passes only when every check passes. Failures don't collapse — three failing checks produce three verdicts, not one.

### Why two categories instead of one

You could imagine pushing everything into acceptance criteria: have the agent emit "requires authentication" on every change. It would technically work, but it's the wrong factoring:

* The agent would have to remember the team's full ruleset on every submission.
* A bad agent could silently omit a rule.
* Changes to the ruleset would require re-instructing the agent.

Invariants live outside the per-change loop. The team encodes them once. They apply automatically. The agent doesn't need to know about them — the verifier loads the matching set itself.

The mirror argument applies to acceptance criteria: not everything can be an invariant. "This endpoint returns these fields" is specific to a change. Forcing it into an invariant would either bloat the ruleset or push too much per-change content into team policy.

The split is: invariants for what's true across changes, criteria for what's specific to this one.

### Overrides and waivers

Sometimes a change legitimately needs to break an invariant. Two mechanisms:

* **Exception declared on the invariant.** The right place for recurring exceptions (e.g. health-check endpoints don't require auth). The exception is part of the rule, applied automatically. See [Invariants — Exceptions](invariants.md#exceptions).
* **Waiver in the review document.** For one-off cases the invariant didn't anticipate. The reviewer waives the verdict with a reason. Recorded in the audit trail as "Waived by `<reviewer>` — `<reason>`."

Waivers don't disable invariants. They acknowledge a failure as accepted, with attribution. If you find yourself waiving the same invariant repeatedly, the rule is wrong — tighten its scope or add the case as an exception.

### Conflicting verdicts

It's possible for an acceptance criterion and an invariant to make conflicting claims about the same code. Common shape:

* Invariant: "All HTTP handlers require auth."
* Criterion: "Endpoint `/webhooks/stripe` does not require auth."

Both verdicts run. Both will appear in the review document — the criterion passes (the endpoint doesn't require auth), the invariant fails (the endpoint should require auth).

This is a feature, not a bug. The reviewer sees both verdicts and decides — usually by waiving the invariant verdict with a reason ("webhook endpoint, validated by signature"). The exception is then logged in the audit trail.

If the same conflict recurs across changes, that's a signal to add the webhook path as an exception on the invariant.

### When to use which

| Use…                | When the rule is…                                                                 |
| ------------------- | --------------------------------------------------------------------------------- |
| Acceptance criteria | Specific to this change. Won't apply to anything else.                             |
| Path-scoped invariant | A pattern in one module — billing, auth, data access — that needs to hold across every change in that area. |
| Repo-level invariant  | A rule that applies to every change in one service.                                |
| Org-level invariant   | A rule that applies everywhere — security baseline, secrets handling, observability. |

The test is recurrence: if you'd have to ask the agent to add the same criterion on the next ten changes, it should be an invariant.

### See also

* [Invariants](invariants.md) — deep concept page
* [How verification works](how-verification-works.md) — the per-check pipeline
* [Setting up org invariants](../setting-up-org-invariants.md)
