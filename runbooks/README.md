---
icon: layer-group
---

# Runbooks

Runbooks offer a framework for spec-driven development that helps standardize AI development for your team. It breaks down any task into step-by-step execution plan that can be reviewed and executed remotely.

With the remote agentic environment that run pre-configured sandboxes, you can run Runbooks in the Aviator cloud or onprem behind your firewall.

This framework allows developers to create, manage, and execute Runbooks to perform various types of coding tasks across your repositories.

Behind the scenes, the framework uses Claude Code to plan and execute the coding tasks.

## Planning

There are two common workflows to plan with Runbooks.

### 1. Custom task

* Share the task requirements through an interactive chat interface
* The planning agents work with Claude code to review the code, fetch the required context and prepare the plan.

### 2. Using a template

* Use an existing Runbook as a template, and build up on it by providing specific context associated with the task at hand.
* The planning agents modify the Runbook based on the provided context

## Collaboration

* Invite other users to collaborate on the Runbook
* Execute specific step or assign it to the other users
* Agents create pull requests, verifies the build steps
* User provides feedback, agents iterate on the results
* PRs are merged after review and verification by the user
* Agents memorize the context and the Runbooks for future use.

{% embed url="https://youtu.be/AHV-T6t1ulk" %}

### Runbooks at a glance

* Intelligent agents powered by Claude or Gemini models
* All modifications are created as small, reviewable PRs
* Multiple developers can work together on Runbooks
* Available as cloud-managed or on-premise installation

### Coding with Agents

There are 3 core workflows within Aviator Agents

1. Planning - collaborate with the agents to work on a Runbook
2. Execution - let agents dry run, execute independent steps or all at once
3. Review - provide feedback via pull requests to iterate with the agents

#### Prerequisites

* Access to the Runbooks dashboard (on-premise or cloud)
* GitHub account with appropriate repository permissions

### Use cases

Although Agents framework can handle most types of engineering tasks, they do well specifically with:

* Simple well defined features
* UI improvements
* Bug fixes
* Refactoring
* Code migrations
* Test coverage improvement
* Improving readability
* Dependency graph simplification
* Flaky test resolution

### Learn more

* [Getting started guide](getting-started.md)
* [On-premise installation](how-to-guides/configuration/on-premise-installation.md)
* [Understanding Runbooks](concepts/runbook-format.md)
