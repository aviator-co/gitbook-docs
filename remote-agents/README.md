---
hidden: true
---

# Remote Agents

The Remote Agentic Environment enables autonomous code changes through AI-powered agents that collaborate with development teams. This platform allows developers to create, manage, and execute Runbooks that define complex code transformations across repositories.

### How it works

A workflow typically looks like the following:

* Share the requirements with the planning agents
* Agents review the code, scan through documentation to prepare a Runbook
* Invite other users to collaborate on the Runbook
* Execute specific step or assign it to the other users
* Agents create pull requests, verifies the build steps
* User provides feedback, agents iterate on the results
* PRs are merged after review and verification by the user
* Agents memorize the context and the Runbooks for future use.

{% embed url="https://www.youtube.com/watch?v=kCKmyqXhW90" %}

### Aviator Agents at a glance

* Intelligent agents powered by Claude or Gemini models
* Agents analyze codebases and identify improvement opportunities
* All modifications are created as small, reviewable PRs
* Multiple developers can work together on Runbooks
* Available as cloud-managed or on-premise installation

### Coding with Agents

There are 3 core workflows within Aviator Agents

1. Planning - collaborate with the agents to work on a Runbook
2. Execution - let agents dry run, execute independent steps or all at once
3. Review - provide feedback via pull requests to iterate with the agents

#### Prerequisites

* Access to the Remote Agentic Environment dashboard (on-premise or cloud)
* GitHub account with appropriate repository permissions
* Supported LLM API credentials (Claude or Gemini). Also supports Bedrock and Vertex.

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
* [On-premise installation](configuration/on-premise-installation.md)
* [Understanding Runbooks](concepts/runbooks.md)
