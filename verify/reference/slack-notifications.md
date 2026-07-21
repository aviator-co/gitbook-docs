# Slack notifications

Verify can notify a PR author in Slack when a verification run finishes, and let them act on the result without leaving Slack — re-run verification, waive a failing invariant, or remove a failing criterion.

This page is a reference for what gets sent, when, and what each control does. For the underlying result shapes, see [Understanding verification results](understanding-verification-results.md).

### Prerequisites

* Your workspace has the Slack integration connected. See [Slack Integration Guide](../../api/personal-integrations.md#initial-slack-setup).
* The recipient has linked their Slack account by running `/aviator connect`, and has associated their GitHub handle with their Aviator user.

### The thread model

Verify keeps **one Slack thread per pull request**, in the PR author's DM with the Aviator app.

* The first notification for a PR posts the thread root.
* Every later result posts as a **reply in that same thread**, so the whole PR reads as one conversation instead of scattering across the DM. Replies aren't broadcast to the DM's main view, but the author is still notified — it's their own DM.
* The **thread root doubles as a live status summary.** After each result — and after any waive or removal — the root is edited in place to reflect the PR's *current* state. It doesn't wait for the next run.

{% hint style="info" %}
Editing the root is best-effort. If your Slack workspace policy forbids message edits, the root summary can go stale. The thread replies are the authoritative record in Slack, and the `aviator/verify` GitHub check is always authoritative overall.
{% endhint %}

### When a notification is sent

Notifications fire only on **terminal** run statuses.

| Run status                              | Behavior                                                                                                     |
| --------------------------------------- | ------------------------------------------------------------------------------------------------------------ |
| `failed`                                | Always sent.                                                                                                 |
| `error`                                 | Always sent — "Verify couldn't complete".                                                                    |
| `passed`                                | Sent **only** if the previous completed run failed, as a recovery message. A first-try pass is silent, and you never get two success messages in a row. |
| `pending`, `in_progress`, `deferred`    | Not sent — no outcome yet.                                                                                   |

Additional rules:

* **At most one notification per run.** A terminal run can be finalized from several paths at once; Verify guarantees a single DM rather than risking a duplicate. The trade-off is that a send that fails is not retried — the GitHub check still carries the verdict.
* **Superseded runs are silent.** A run cancelled because a newer run replaced it isn't an outcome worth reporting.
* A run enqueued as `deferred` (waiting on baseline-invariant selection) produces no separate message. It posts its result in the thread once it actually runs.

### Anatomy of the message

A failure summary looks like this:

```
❌ Verify failed on acme/api#482

3 of 7 criteria failed · ⚪ 1 waived · commit a1b2c3d · triggered by push

Acceptance criteria
  2. Returns 429 when the rate limit is exceeded                      ⋮
     src/api/limiter.go:88 — Handler responded 200 on the 11th request…

Baseline invariants
  5. auth-required-on-handlers                                        ⋮
     src/handlers/admin.go:23 — Handler does not call auth middleware…

…and 1 more — view all in Aviator

[ 🔁 Re-run ]
```

#### Headline

| Condition                                                | Headline                        |
| -------------------------------------------------------- | ------------------------------- |
| Run passed                                               | ✅ **Verify passing** on _PR_    |
| Run failed, and every remaining failure has been waived  | ✅ **Verify passing** on _PR_    |
| Run failed with unwaived failures                        | ❌ **Verify failed** on _PR_     |
| Run errored                                              | ⚠️ **Verify couldn't complete** on _PR_ |

{% hint style="info" %}
**Passing with exceptions.** When every remaining failure is waived, the run's own status stays `failed` — not every criterion literally passed — but the merge gate is green. Slack renders this as **passing** so it never contradicts the `aviator/verify` check for the same run.
{% endhint %}

#### Context line

A ` · `-joined summary under the headline:

* **Counts** — `X of Y criteria passing` on a pass, `X of Y criteria failed` on a failure, or `last run couldn't complete — not caused by your code` on an error.
* **Waived count** — shown only when at least one criterion is waived.
* **Commit** — the short SHA, when known.
* **Trigger** — a readable label for what started the run, such as `triggered manually` or `triggered by approval`. See [When a run is triggered](../concepts/how-verification-works.md#when-a-run-is-triggered). A trigger with no label falls back to `triggered automatically`.

#### Failure listing

* Only **active, unwaived** failures are listed. A criterion that was waived or deleted since the run drops out, matching the merge gate.
* Failures are split into **Acceptance criteria** (the PR's own) and **Baseline invariants** (the org's), in that order. An empty group is omitted.
* Each entry is numbered by the criterion's position in the acceptance-criteria list. **These numbers match the GitHub check run**, so you can carry a number between surfaces.
* **At most 3 failing criteria** are rendered. The rest collapse into `…and N more — view all in Aviator`.
* Each entry shows `file:line` when the verdict carried a location, and the reason, **truncated to 120 characters**.

Full evidence is never inlined in Slack — follow the link to the review document for it.

### Actions

| Control              | Where it appears                                              | What it does                                    |
| -------------------- | ------------------------------------------------------------- | ----------------------------------------------- |
| 🔁 **Re-run**        | Failed and errored summaries only                             | Re-runs verification on the PR                  |
| ⋮ **Waive failure…** | Next to a failing **baseline invariant** criterion            | Opens a modal to waive with a category + reason |
| ⋮ **Remove criterion…** | Next to a failing **acceptance criterion**                 | Opens a confirmation modal to delete it         |

The ⋮ menu offers only the action that's legal for that criterion's type — invariant criteria can be waived but not deleted, and everything else can be deleted but not waived.

Every action — success, refusal, or error — posts a visible reply in the thread. Nothing fails silently.

#### 🔁 Re-run

Enqueues a fresh verification run for the PR and replies in the thread:

```
🔄 Re-running verification — results will post in this thread.
```

Rapid double-clicks and Slack re-deliveries collapse into a single run. If the account no longer has Verify enabled, or the enqueue fails, the reply explains why instead.

#### Waive failure

Opens a modal with two required fields:

| Field             | Notes                                                     |
| ----------------- | --------------------------------------------------------- |
| **Category**      | The standard waiver categories — see [Invariants](../concepts/invariants.md#waivers). |
| **Justification** | Free text. A whitespace-only value is rejected inline and the modal stays open. |

On submit, Verify records the waiver, refreshes the run's counts, re-posts the `aviator/verify` check so the merge gate can unblock, confirms in the thread, and re-renders the thread root:

```
✅ @user waived Handler does not call auth middleware (Accepted risk) — shipping
behind a feature flag, follow-up in ENG-2214.
```

Semantics worth knowing:

* A waiver is an **overlay**. The underlying verdict keeps its engine result for audit — see [Audit trails and compliance](../concepts/audit-trails-and-compliance.md).
* Waiving is **idempotent**. Waiving an already-waived criterion returns the existing waiver rather than erroring.
* Only **invariant-derived** criteria can be waived. Task criteria are rejected with a pointer to edit or delete them instead.
* If the waived criterion was the last failure, the merge gate flips to passing and the root re-renders as passing.

#### Remove criterion

A pure confirmation modal — no inputs. On submit, the criterion is removed from the runbook's acceptance criteria and future runs won't check it:

```
@user removed Returns 429 when the rate limit is exceeded — future verify runs
won't check it.
```

Semantics worth knowing:

* Removal **creates a new version** of the criteria set containing the survivors. The old version isn't destroyed; history is preserved.
* The run's counts are recomputed and the `aviator/verify` check is re-posted.

It's refused in these cases:

| Condition                                          | Message                                                            |
| -------------------------------------------------- | ------------------------------------------------------------------ |
| The criterion came from a baseline invariant       | Baseline invariant criteria cannot be deleted — waive it instead.  |
| The criterion is no longer in the active set       | Those acceptance criteria are no longer active in this session.    |
| It's the last non-invariant criterion              | Cannot delete the last non-invariant criterion.                    |
| The session has no active runbook                  | No active runbook found for this session.                          |

### Old messages still work

Buttons act on the PR's **current** state, not the state captured when the message was posted. Clicking Re-run on a months-old message verifies the PR as it is today, and criterion controls resolve through the criterion's history — so a control built for an older run still targets the right criterion even though editing the criteria re-mints them each time.

### Who can use the actions

**Re-run** requires no Aviator identity — anyone who can see the DM can click it. It only re-runs verification.

**Waive** and **Remove** require the clicking Slack user to resolve to an Aviator user **on the same account** as the runbook. If it can't, the action is refused in-thread:

```
⚠️ Couldn't waive — your Slack account isn't linked to an Aviator user on this
account. Run /aviator connect first.
```

All three actions re-check that Verify is enabled for the account **at click time**, not just when the message was rendered. A message posted while Verify was enabled will refuse its actions after Verify is turned off.

Requests are verified against the Slack signing secret, and a click arriving from a different Slack workspace than the one that sent the message is rejected.

### Notification settings

Verify notifications are sent to the PR author as a Slack DM and are enabled by default. Both the failure DM and the recovery DM are sent this way.

Per-event opt-out for Verify notifications is not currently available in Aviator settings. To stop all Aviator DMs, disconnect your Slack account under `Settings > Personal > Integrations`.

### Troubleshooting

| Symptom                                              | Likely cause                                                                                     |
| ---------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
| No DM at all                                         | Slack isn't connected for the account, the user hasn't run `/aviator connect`, or their GitHub handle isn't associated with their Aviator user. |
| The run passed but no DM arrived                     | Expected. Passes are only announced when they follow a failure.                                  |
| The thread root looks stale                          | Your workspace forbids message edits. Read the thread replies, or open the run in Aviator.       |
| Buttons do nothing / refuse                          | Verify is no longer enabled for the account, or your Slack account isn't linked to an Aviator user on this account. |
| Slack shows passing, but a criterion still shows failed in the UI | Every remaining failure is waived. The merge gate is green; see [Passing with exceptions](#headline). |

### See also

* [Understanding verification results](understanding-verification-results.md)
* [Fixing verification failures](../how-to-guides/fixing-verification-failures.md)
* [GitHub integration](github-integration.md)
* [Concepts: Invariants](../concepts/invariants.md)
* [Slack Integration Guide](../../api/personal-integrations.md)
