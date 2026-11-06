# Decisions

A decision records a call behind a pull request that a reviewer could reasonably ask "why?" about. Each one belongs to a single review, is either open or settled, and has a comment thread. Every entry says who made it, and whether that was a person or an agent.

To read, answer, or record decisions, see [Work with decisions](../how-to-guides/working-with-decisions.md). For why they matter to review, see [How Verify reviews a change](review-model.md).

### What counts as a decision

A decision is a good fit for:

* An approach picked over another one.
* Behavior that looks wrong but is deliberate.
* A risk someone saw and shipped anyway.
* Work left for a later PR.
* A question that needs an answer before or during review.

It's a poor fit for what the diff already says, mechanical work, or dead ends that left no trace in the code. A handful per PR is normal. A dozen usually means it's narrating the work.

Decisions sit next to a few similar things:

| Thing | What it does | How a decision differs |
| ---------------------------- | ------------------------------------------------------------ | ----------------------------------------------------------- |
| Acceptance criterion | States what the code must do. Verify judges it. | Says why it was built this way. Nothing judges it. |
| [Invariant waiver](invariants.md) | Clears a failed invariant verdict for this PR. | Doesn't change any verdict or the Verify check. |
| PR or code comment | Discusses a line or the PR. | Has a type, an outcome, a reason, and separate credit for people and agents. |

### What a decision holds

| Field | Description |
| ------------ | ------------------------------------------------------------------------------------------------ |
| `type` | The kind of decision. See below. |
| `title` | The question it answers, in one line, readable without any other context. |
| `summary` | Optional. What was decided or what's being asked, in a sentence or two. |
| `anchor` | Optional. The commit, file, and lines it's about. Returned by the CLI and API, not shown on the review page. |
| `outcome` | How it was settled. Empty while it's open. |
| `resolution` | Why it was settled this way. Required to settle. |
| Comments | The thread, in order, showing how the call was reached. |

#### Types

| Type | Use it for |
| -------------- | --------------------------------------------------- |
| `question` | Something that needs an answer. |
| `possible_bug` | Behavior that may be wrong. |
| `risk` | Something that could go wrong after this ships. |
| `choice` | One option picked over another. |

#### Outcomes

| Outcome | Meaning |
| --------------- | ------------------------------------------------ |
| `decided` | A choice was made or a question was answered. |
| `fixed` | It was a bug, and the change fixes it. |
| `intended` | It looks wrong, but it's deliberate. |
| `accepted_risk` | The risk is real, and it's shipping anyway. |
| `follow_up` | The call is to do the work later. |
| `withdrawn` | The concern turned out to be wrong. |

`follow_up` and open aren't the same. A `follow_up` is settled: someone decided to do the work later. An open decision is one nobody has made yet.

### Lifecycle

A decision is **open** until someone **settles** it with an outcome and a resolution. It can be created already settled, which is how most decisions recorded after the fact start.

Settling isn't final. Anyone can **reopen** a settled decision, and they must add a comment saying why. Reopening clears the outcome and the resolution, and someone settles it again later.

* A settled decision can't change until it's reopened. Comments can still be added.
* Every change to a decision adds a new version, with who wrote it. Aviator keeps all of them, and the page and API show the current one.
* A comment can be edited by the person who wrote it. The edit replaces the text, and the earlier text isn't kept. A change of mind should be a new comment or a reopen rather than an edit.

### Identity

Decisions keep humans and agents apart so you can tell which positions a person took and which ones an agent took on its own.

#### Every action has its own credit

Raising a decision, changing it, settling it, reopening it, and commenting on it are each credited separately. One decision can have an agent raising it, a person and an agent discussing it, and a person settling it, and the record shows each of those.

Each action records two things:

| Recorded | Example | Where it comes from |
| --------- | ------------------------------- | ---------------------------------------------- |
| Account | The Aviator user behind the request | The credential on the request. The client can't choose it. |
| Role | `person`, `coding_agent`, `review_agent` | Declared by the client. |

For agents, the client can also declare `model`, `harness`, and `skills`. The CLI and API return these.

#### What each role means

| Role | Meaning | Example |
| -------------- | -------------------------------------- | ------------------------------------------------------------------------- |
| `person` | A person took this position. | The user told their agent to ship without the backfill. |
| `coding_agent` | The coding agent took this position. | The agent picked an approach, and the user didn't weigh in. |
| `review_agent` | A review agent took this position. | A review subagent objected to the approach. |

The rule is to credit whoever made the call:

* **A person's call is credited to the person, even when an agent raised the question.** The agent adds its side as its own comment.
* **An agent's call stays credited to the agent, even when a person saw it and let it stand.** A person's silence isn't their decision.

So a reviewer can read a settled decision like this:

| Settled by | What it tells you |
| -------------------------- | ----------------------------------------------------------------- |
| `person` | A person made this call. |
| `coding_agent` | An agent made this call, and no person is on record as making it. |
| `review_agent` | A review agent made this call, for example by withdrawing its own finding. |

#### How it looks on the review page

Agents don't have their own accounts. They work under the account of the person who signed in, so the same name can appear on both human and agent entries. A badge marks the difference:

| Entry | Shown as | Meaning |
| -------------------------------------------------- | --------------------------- | ------------------------------ |
| Raised "Rate limit per account or per repo?" | Sam Lee `coding agent` | Sam's agent raised it. |
| Comment recommending per account | Sam Lee `coding agent` | The agent's recommendation. |
| Comment saying per repo breaks monorepo teams | Sam Lee | Sam's position. |
| Settled as `decided` | Sam Lee | Sam made the call. |

An entry with no badge is credited to a person. The page shows who raised the decision, who settled it, and who wrote each comment.

#### What the role doesn't prove

The account is authenticated. The role isn't.

* **`person` means the call was the person's. It doesn't mean these are their exact words.** When [`aviator-decide`](../how-to-guides/recording-decisions.md) records from a session, the agent decides whether a call was the person's, credits it as `person`, and paraphrases what they said. A comment typed on the review page is also `person`, and the page shows both the same way.
* **Any client with a valid credential can declare any role.** Treat the role as a record of who made the call. Don't treat it as proof.
* **`person` isn't an approval.** Settling a decision doesn't approve the PR or count as review sign-off.

Writes accept a web session, the `aviator login` credential, or a user access token, since each entry has to land under a person's account. Account API tokens can read decisions but can't write them, and other credentials are refused.

### Author and reviewer

Author and reviewer are positions on a PR. Verify doesn't store either one on a decision.

|  | Author and their coding agent | Reviewer and any review agent |
| ---------------- | ------------------------------------------------------- | ------------------------------------------------------ |
| Job | Record the calls behind the change and answer what's open. | Judge those calls without having to reconstruct them. |
| Usual actions | Raise, comment, settle with a reason. | Read, comment, reopen, raise concerns. |
| Credited as | `person` for the author, `coding_agent` for the agent. | `person` for the reviewer, `review_agent` for the agent. |

The usual pattern is that the author settles and a reviewer who disagrees reopens. That's a convention and Verify doesn't enforce it, so a reviewer can settle a decision and an author can reopen one.

Two terms are easy to mix up:

* **`author` in a batch or an API response means who wrote that entry.** It doesn't mean the PR's author. A reviewer's comment has the reviewer's role as its `author`.
* **`review_agent` isn't the human reviewer.** A human reviewer is recorded as `person`, the same as the PR's author. To tell them apart, compare the account on the entry to the PR's author.

### Permissions

Permissions follow the account, never the role. An agent working under your account can do what you can do, including editing a decision you raised.

| Action | Who can do it |
| ------------------------------------------ | ---------------------------------------------------- |
| Raise, settle, reopen, comment | Any Verify user on the account. |
| Change type, title, summary, or anchor | Only the user who raised the decision, and only while it's open. |
| Edit a comment | Only the user who wrote it. |

### What decisions don't do

* **They don't affect the Verify check or merging.** An open decision doesn't block anything.
* **They don't get a verdict.** Verify doesn't check them against the code.
* **They don't sync with GitHub.** They aren't posted to the PR and don't appear in the [Verify tab on the PR](../how-to-guides/verify-on-github.md).
* **They don't notify anyone.** Nobody hears about a new or reopened decision, so check the review.
* **They aren't in `aviator results`.** Read them with `aviator decisions list`.

The chat on the review page can read a review's decisions and their comments, so you can ask it about them.

### See also

* [Work with decisions](../how-to-guides/working-with-decisions.md)
* [Record decisions with aviator-decide](../how-to-guides/recording-decisions.md)
* [How Verify reviews a change](review-model.md)
* [Audit trails and compliance](audit-trails-and-compliance.md)
* [Aviator CLI: `aviator decisions`](../reference/cli.md#aviator-decisions)
