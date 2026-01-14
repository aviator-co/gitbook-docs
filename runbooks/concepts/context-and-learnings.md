# Context and learnings

Runbooks automatically learn from your coding sessions to provide better assistance over time. This document explains how context works and how it benefits your workflow.

## Types of context

Runbooks use two types of context:

| Type | Source | Purpose |
|------|--------|---------|
| **Learnings** | Automatically captured from sessions | Remember solutions to problems |
| **Context Files** | Manually created by you | Document architecture and conventions |

Both types are shared across your account, so knowledge captured by one team member benefits everyone.

## What are learnings?

Learnings are reusable insights captured from your Runbook sessions. When an agent encounters a problem and finds a solution, it remembers that pattern so it can apply the same fix in future sessions.

**Example:**

```
Pattern:  "pytest fails with ModuleNotFoundError for local packages"
Solution: "Run `pip install -e .` to install the package in editable mode"
Applies to: pytest, Python projects
```

The next time anyone on your team hits a similar issue, the agent already knows how to fix it.

## When are learnings captured?

Learnings are captured during high-signal moments — times when something notable happens that's worth remembering:

| Moment | What's Captured |
|--------|--------------------|
| **Errors resolved** | Problems encountered during execution and how they were fixed |
| **CI failures fixed** | Build/test failures and the changes that resolved them |
| **Review feedback addressed** | Code improvements made based on reviewer comments |
| **User corrections** | When you modify a Runbook or provide feedback to improve the approach |

Routine operations that complete without issues don't generate learnings — only moments where something was learned.

## How are learnings used?

When you start a new Runbook session, agents automatically retrieve relevant learnings based on:

- **Files you're working with** — Learnings about specific file types or paths
- **Frameworks in use** — Learnings related to React, pytest, Docker, etc.
- **Commands being run** — Learnings about specific build tools or scripts

Relevant learnings appear as helpful hints when planning your task, helping you avoid known pitfalls.

## Learnings lifecycle

```
┌─────────────────────────────────────────────────────────────┐
│  1. CAPTURE                                                 │
│     During execution, notable problems and solutions        │
│     are identified and saved                                │
└─────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────┐
│  2. STORE                                                   │
│     Learnings are stored per-account, deduplicated,         │
│     and tagged with relevant context (files, frameworks)    │
└─────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────┐
│  3. RETRIEVE                                                │
│     When starting a new task, relevant learnings are        │
│     automatically matched based on your current context     │
└─────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────┐
│  4. APPLY                                                   │
│     Learnings surface as guidance during planning,          │
│     helping avoid known issues before they occur            │
└─────────────────────────────────────────────────────────────┘
```

## What makes a good learning?

Learnings are most valuable when they're:

- **Specific** — Clear problem description with exact error messages
- **Actionable** — Concrete solution that can be applied
- **Reusable** — Applicable beyond the original task
- **Project-relevant** — Specific to your codebase patterns and conventions

Generic knowledge (like "run npm install to install dependencies") is not captured — only insights specific to your project.

## Improving over time

The learning system improves with use:

- **Frequency tracking** — Learnings that help repeatedly are prioritized
- **Recency weighting** — Recent learnings are more relevant than old ones
- **Pattern matching** — Similar problems are grouped together

The more you use Runbooks, the smarter they become for your specific codebase.

## What are context files?

Context files are markdown documents you create to describe your project. Unlike learnings (which are captured automatically), context files are authored by you to provide foundational knowledge about:

- Project architecture and conventions
- API patterns and data models
- Testing requirements
- Deployment procedures
- Team-specific coding standards

See [Context Management](../how-to-guides/context-management.md) for how to create and manage context files.

## Privacy

- Learnings and context files are scoped to your account — never shared across organizations
- Learnings don't contain sensitive data like secrets or credentials
- You can view and manage all learnings in the Context tab

---

**Next:** [Managing Context](../how-to-guides/context-management.md) — Learn how to browse, create, and manage learnings and context files.
