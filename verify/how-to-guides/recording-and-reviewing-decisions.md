# Recording and reviewing decisions

A diff shows what changed. It doesn't show what you and your agent weighed and dropped, which risks you accepted, or which questions you settled along the way. Decisions keep that reasoning on the review, so whoever reads the pull request next can see why the code looks the way it does.

Decisions are for the people and agents reviewing the change. They aren't part of verification, so recording one doesn't change a verdict.

### What a decision is

A decision is a choice, risk, possible bug, or question about a review's pull request. It has a title, a summary, and a comment thread, and it can point at specific lines. It stays open until someone settles it with an outcome and a reason.

| Outcome         | Meaning                                       |
| --------------- | --------------------------------------------- |
| `decided`       | A choice was made or a question was answered. |
| `intended`      | The behavior looks wrong but is deliberate.   |
| `accepted_risk` | The risk was seen and shipped anyway.         |
| `fixed`         | The bug was found and fixed in this change.   |
| `follow_up`     | The work was left for a later pull request.   |
| `withdrawn`     | The concern turned out to be wrong.           |

Every decision and comment is credited to whoever made that call: you, your coding agent, or a review agent. An agent writes under the account of the person running it, and its role is shown beside their name.

### Record decisions

Your agent records decisions with the `decisions` skill from the [Aviator agent plugins](https://github.com/aviator-co/agent-plugins). It happens in two ways:

* **With your acceptance criteria.** `/verify-submit` drafts the decisions from your session and shows them next to the criteria, so you check everything at once. Your confirmation covers both.
* **After more work.** Run `/aviator:decisions`, or ask your agent to record the decisions behind the PR. It adds only what isn't recorded yet.

The agent works from the conversation, not the diff, since the conversation is where the reasons are. If a session has nothing a reviewer would ask about, it records nothing.

Read the drafts before you confirm. Nothing is recorded until you do, and these three things hold once it is:

* They go out under your name, and comments credited to you are written in your voice.
* Anyone with access to the review can read them, so the agent leaves out secrets, customer details, and raw production output.
* A decision can't be deleted. You can edit a comment to fix a wrong detail, and a change of mind is a new comment or a reopen.

### Review a PR's decisions

Before you judge someone else's pull request, look at what's already been decided on it. Open decisions are listed first on the review page. In a coding agent, ask "what's been decided on this PR?". If you've set up [agent hooks](set-up-agent-hooks.md), your agent is reminded to look when it pulls up a PR to review.

Treat each decision as a claim, not a fact. Agents wrote many of them, and the code may have moved since.

| What you find                                       | What to do                                                                                                     |
| --------------------------------------------------- | -------------------------------------------------------------------------------------------------------------- |
| Settled, and it holds against the code              | It's closed. Don't raise it again.                                                                             |
| Settled, and you disagree or the code contradicts it | Raise it, and say what the decision missed. If a person made the call, it may rest on context the code doesn't show, so ask. |
| Open, raised by the author                          | It's a question for reviewers. Answer it.                                                                      |
| Open, raised by a review                            | It's a question for the author. Leave it to them.                                                              |

To respond on the record, comment on the existing decision, or reopen it if you've overturned it. Raise a new one only for something nobody has weighed yet. Anyone can settle, reopen, and comment. Only the user who raised a decision can change its type, title, summary, or anchor, and a settled decision has to be reopened first.

### From the CLI

Agents use these commands, and you can too. Find a pull request's review, whoever created it, then read its decisions:

```bash
aviator sessions --repo myorg/myrepo --pr 1234
aviator decisions list r/123
aviator decisions show r/123 7
```

See [`aviator decisions`](../reference/cli.md#aviator-decisions) for recording them.
