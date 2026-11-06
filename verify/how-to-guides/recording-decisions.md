# Record decisions with aviator-decide

`/aviator-decide` is a skill that reads your agent session and records the calls behind your PR as [decisions](../concepts/decisions.md) on its review. The reasoning stays in that session otherwise, where no reviewer can see it.

### Before you start

* Install the Aviator CLI (v0.0.29 or later) and the Aviator skill. See [Before you start](working-with-decisions.md#before-you-start).
* The branch needs a review. Run `/verify-submit` first if it doesn't have one.

### Run it

After the PR is open, run this in the same agent session that did the work:

```
/aviator-decide
```

It only knows what's in that conversation, so run it before you close the session. It doesn't ask before recording. It then shows you what it recorded, grouped by branch, and names anything left open.

You can also ask your agent in plain words, such as "record the decisions behind this PR".

### What gets recorded

The skill looks for things a reviewer could ask "why?" about, and that the session answered:

| What happened in the session | Type | Outcome |
| --------------------------------------------------- | -------------------------- | --------------- |
| Picked one approach over another | `choice` | `decided` |
| Kept behavior that looks wrong but is deliberate | `possible_bug` or `choice` | `intended` |
| Saw a risk and shipped it anyway | `risk` | `accepted_risk` |
| Found a bug in the change and fixed it | `possible_bug` | `fixed` |
| Left something for a later PR | any | `follow_up` |
| Raised a concern that turned out wrong | any | `withdrawn` |
| Asked a question the user answered | `question` | `decided` |
| Still unresolved | any | left open |

It leaves out what the diff already says, dead ends that left no trace, typos and lint fixes, and anything already recorded on the review. A fixed bug only earns a place if a reviewer would wonder about the fix, or if the same mistake is easy to make again.

Expect a handful per PR, and few of them left open. A reviewer should be able to get through the list in a minute.

### Who gets credit

Each decision and comment is credited to whoever made that call. See [Identity](../concepts/decisions.md#identity) for what this means.

| In the session | Recorded as |
| ------------------------------------------------------------ | ------------------------------------------------- |
| You picked between options the agent laid out | Settled as `person`, with the agent's side as its own comment |
| You told the agent to keep some behavior | Settled as `person` |
| The agent picked an approach, and you saw it and said nothing | Settled as `coding_agent` |
| A reviewer subagent objected | A comment as `review_agent` |
| A question nobody answered | Left open, credited to whoever raised it |

`person` entries land under your account, and the skill never writes your name. Agent entries carry the model, the harness, and every skill loaded in the session.

If the agent picked something you'd want on record as your call, say so in the session before running the skill, and it's credited to you.

### The comment thread

Comments show how the session reached the call: what was proposed, what pushed back, what changed direction. Each one traces to something actually said or shown in the session, and the skill doesn't invent turns or objections.

Comments are written in the author's voice, in first person. Your words are reworded to the point a reviewer cares about and are never quoted. The agent's private reasoning stays out.

A simple call needs no comment, or one naming the alternative and why it lost. A call that went back and forth usually gets three to six.

### Stacked PRs

Each PR has its own review, and reviewers only see that review's decisions. The skill finds the review for each branch the session worked on, and puts each decision on the branch whose diff holds the code it's about. A decision that spans several branches goes on the lowest one.

A branch with no review has nowhere to record yet. The skill tells you, suggests `/verify-submit` for it, and records the others.

### Later in the session

* **You settle an open decision.** Tell the agent your call and why, and it records it as you right away.
* **A comment has a wrong detail.** The agent can edit a comment it recorded.
* **You changed your mind.** The agent reopens the decision or adds a comment. It doesn't rewrite what was recorded.
* **More work landed.** Run `/aviator-decide` again. It reads what's recorded and adds only what's new.

### See also

* [Work with decisions](working-with-decisions.md)
* [Concepts: Decisions](../concepts/decisions.md)
* [Aviator agent plugins](https://github.com/aviator-co/agent-plugins)
