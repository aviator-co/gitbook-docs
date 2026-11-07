# How Verify reviews a change

Verify gives a reviewer what they need to trust a change without rebuilding the author's reasoning from the diff. This page covers what that is, where each part comes from, and what's left for a person to judge.

### The problem with reviewing the diff alone

A diff shows what changed. It doesn't show what the change was for, whether it works, or why it was built this way.

A reviewer used to get the rest by assuming the author had thought through each line, and by asking them about the ones that looked odd. With a coding agent, the author may have directed the work without reading every line, and the reasoning stays in a session nobody else sees.

### Four questions

Verify splits review into four questions and answers each one separately.

| Question | Answered by | Where it comes from |
| ---------------------------------------------------- | ---------------------------------------------- | -------------------------------------------------- |
| What is this change for? | [Intent](../how-to-guides/writing-effective-acceptance-criteria.md) | The author and their agent |
| Does it do that? | Acceptance criteria, verdicts, and evidence | Criteria from the author and their agent. Verdicts and evidence from Verify |
| Does it break our standing rules? | [Invariants](invariants.md) | The team's catalog. Verdicts and evidence from Verify |
| Why is it built this way, and who made the call? | [Decisions](decisions.md) | The author, their agent, reviewers, and review agents |

The first three tell you whether the change does what was agreed. The last one tells you whether what was agreed, and how it was built, was the right call.

### Why decisions are the core

Verification checks claims. It can show that the API returns 429 once a client's budget is spent. It can't say whether the budget should be per account or per repo, or whether it's fine to ship without a backfill. Those calls are what's left for a person to judge, and they're where a reviewer's time is best spent.

A decision records one of those calls: the question, the outcome, the reason, and who made it. Humans and agents are credited separately on every entry, so a reviewer can see which positions a person took and which ones an agent took on its own. See [Identity](decisions.md#identity).

That split is how a reviewer decides where to look. A risk a person accepted and a risk an agent accepted without anyone weighing in need different amounts of attention.

### Author and reviewer

The **author** owns the PR and works with a coding agent. They record the calls behind the change and answer what's still open.

The **reviewer** judges those calls, usually along with the verdicts and evidence. They read what's recorded, push back by commenting or reopening, and approve when they're satisfied.

See [Author and reviewer](decisions.md#author-and-reviewer) for how these positions relate to what Verify records.

### How a review goes

1. **The author builds the change** with their agent.
2. **The agent submits the intent and criteria** with `/verify-submit`, which creates the review. It also [records the decisions](../how-to-guides/recording-decisions.md) the author confirms.
3. **The author opens the PR.**
4. **Verify runs** when the PR is marked ready for review, or when someone starts a run. See [When a run is triggered](how-verification-works.md#when-a-run-is-triggered).
5. **The reviewer reads the review page.** They start with the intent, then read open decisions and failed verdicts, then settled decisions. The evidence and diff are there for whatever needs a closer look.
6. **The reviewer and author go back and forth.** The reviewer comments on or reopens decisions, and the author or their agent answers and settles them. See [Work with decisions](../how-to-guides/working-with-decisions.md).
7. **The reviewer approves or waives** with a reason, and the PR moves on to your normal merge flow.

### What Verify doesn't establish

* **A criterion nobody wrote isn't checked.** Part of a review is asking whether the criteria cover the right things.
* **A verdict is a judgment made from evidence, and it can be wrong.** Read the evidence for the criteria you care about most. A criterion judged by code scan hasn't been run, even when it passes.
* **A call nobody recorded doesn't appear.** No decisions on a review doesn't mean no calls were made.
* **Decisions aren't verified.** Verify doesn't check that a decision's summary or resolution is true of the code.
* **The diff still matters.** Verify changes where a review starts. Read the code wherever your judgment needs it.

### When Verify isn't worth running

Typo fixes, documentation updates, and small config changes usually don't justify a run. Verification is enabled per repository, and draft PRs are left alone until they're marked ready for review.

### See also

* [Decisions](decisions.md)
* [How verification works](how-verification-works.md)
* [Work with decisions](../how-to-guides/working-with-decisions.md)
* [Audit trails and compliance](audit-trails-and-compliance.md)
