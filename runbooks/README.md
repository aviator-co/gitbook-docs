---
icon: layer-group
---

# Runbooks

Runbooks is a multiplayer-AI coding framework that leverages spec-driven development. It breaks down any task into step-by-step execution plan that can be reviewed and executed remotely.

With the remote pre-configured sandboxes, you can run Runbooks in the Aviator [cloud](concepts/cloud-sandboxes.md) or [onprem](../manage/on-premise-installation/) behind your firewall. Behind the scenes, the framework uses Claude Code to plan and execute the coding tasks in these sandboxes.

This framework enables developers to invite other stakeholders to plan together, and it leverage Claude Code agents to research, plan and execute assigned tasks.

Tasks assigned to Runbooks can be executed as [step-by-step-execution.md](how-to-guides/step-by-step-execution.md "mention") or as [one-shot-mode.md](concepts/one-shot-mode.md "mention") depending on the complexity of the task.

## Planning

There are two common workflows to plan with Runbooks.

### 1. Custom task

Share the task requirements through an interactive chat interface. The planning agents work with Claude code to review the code, fetch the required context and prepare the plan.

### 2. Using a template

Use an existing [Runbook as a template](concepts/templates.md), and build up on it by providing specific context associated with the task at hand. The planning agents modify the Runbook based on the provided context.

## Collaboration

Using Runbooks' multiplayer mode you can:

* [Invite other users](concepts/collaborating-with-the-team.md) to collaborate on the Runbook
* Execute specific step or assign it to the other users
* Agents create pull requests, verifies the build steps
* User provides feedback, agents iterate on the results
* PRs are merged after review and verification by the user
* Agents memorize the context and the Runbooks for future use.

{% embed url="https://youtu.be/AHV-T6t1ulk" %}

#### Prerequisites

* Access to the Runbooks dashboard (on-premise or cloud)
* GitHub account with appropriate repository permissions

### Use cases

Although Agents framework can handle most types of engineering tasks, they do well specifically with:

* [Product backlog](concepts/use-cases/product-backlog.md)
* [Refactoring](concepts/use-cases/code-refactoring.md)
* [Bug fixes](concepts/use-cases/bug-fixes.md)
* [Improving build times](concepts/use-cases/improve-build-times.md)
* [Flaky test resolution](concepts/use-cases/flaky-test-resolution.md)
* [Code migration](concepts/use-cases/code-migrations.md)
* [Improving readability](concepts/use-cases/improving-readability.md)
* [Improving code coverage](concepts/use-cases/improving-code-coverage.md)

### Learn more

* [getting-started.md](getting-started.md "mention")
* [on-premise-installation](../manage/on-premise-installation/ "mention")
* [personal-integrations.md](../api/personal-integrations.md "mention")
