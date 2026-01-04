---
icon: layer-group
---

# Runbooks

Runbooks uses Claude Code agents to plan and execute coding tasks in isolated sandboxes. Describe what you want, review the generated spec, and let agents implement it.

## Why Runbooks

Individual AI tools like Claude Code are powerful but create challenges at team scale: conversations disappear when you close the browser, each developer gets different solutions to the same problem, and implementation knowledge stays siloed.

Runbooks solves this with three capabilities:

**Multiplayer AI**: Multiple developers collaborate in shared sessions. Senior engineers guide juniors, teams align on approaches together, and everyone sees the AI's reasoning. No more inconsistent patterns from isolated AI conversations.

**Persistent knowledge**: Every solution becomes an organizational asset. Context files teach agents your codebase patterns. Successful runbooks become templates that capture proven workflows. New team members leverage accumulated knowledge immediately.

**Spec-driven execution**: Review and approve plans before any code changes. Step-by-step execution with clear milestones replaces ad-hoc prompting. Standardized templates ensure consistent approaches across teams.

## Quick start

1. [Create your first Runbook](getting-started.md)
2. Review and refine the generated plan
3. Execute steps and merge the resulting PRs

## How it works

**Planning**: Describe your task in the chat interface. Agents analyze your codebase, gather context, and generate a step-by-step plan.

**Execution**: Run the plan [step-by-step](how-to-guides/step-by-step-execution.md) with review at each stage, or use [one-shot mode](concepts/one-shot-mode.md) for simpler tasks.

**Collaboration**: [Invite team members](concepts/collaborating-with-the-team.md) to review plans, provide feedback, and assign steps.

## Sandboxes

Agents execute in isolated environments with your codebase:

- [Cloud sandboxes](concepts/cloud-sandboxes.md) - Managed by Aviator, no setup required
- [SSH sandboxes](concepts/ssh-sandboxes.md) - Self-hosted on your infrastructure

## Use cases

- [Code migrations](concepts/use-cases/code-migrations.md) - Framework upgrades, API changes
- [Bug fixes](concepts/use-cases/bug-fixes.md) - Investigate and fix issues
- [Refactoring](concepts/use-cases/code-refactoring.md) - Improve code structure
- [Test coverage](concepts/use-cases/improving-code-coverage.md) - Add missing tests
- [Flaky tests](concepts/use-cases/flaky-test-resolution.md) - Fix unreliable tests
- [Build optimization](concepts/use-cases/improve-build-times.md) - Speed up CI

## Configuration

- [Context files](how-to-guides/context-management.md) - Help agents understand your codebase
- [MCP servers](how-to-guides/mcp-servers.md) - Add custom tool integrations
- [Tool permissions](how-to-guides/claude-code-tools.md) - Control agent capabilities
- [Personas](how-to-guides/persona-management.md) - Customize agent behavior
- [Templates](concepts/templates.md) - Reuse runbooks across tasks

## Prerequisites

- GitHub account with repository access
- Runbooks access ([cloud](https://app.aviator.co) or [on-premise](../manage/on-premise-installation/))

{% embed url="https://youtu.be/AHV-T6t1ulk" %}
