# Architecture Overview

Runbooks consist of several interconnected components that work together to enable autonomous code transformations.

<figure><img src="../../.gitbook/assets/Aviator Runbooks overview.png" alt=""><figcaption></figcaption></figure>

#### Core Components

**API Gateway**

* Handles user authentication and request routing
* Manages session state and security
* Interfaces with the web dashboard

**Orchestration Layer**

* Manages agent container lifecycle
* Distributes tasks across available workers
* Monitors execution progress and health

**Sandbox**

* Aviator supports both cloud and self-hosted sandboxes
* These sandboxes run claude code to perform the operations.

**Web Dashboard**

* Chat interface for planning and executing tasks
* Runbook search and management
* Context management with MCP and documentation support

#### Data Flow

1. User submits task through dashboard
2. API Gateway authenticates and forwards to orchestrator
3. Orchestration layer assigns task to available sandbox container
4. Sandbox container setup GitHub repository and runs agents (Claude code)
5. These agents to analyze code via GitHub API/Git protocols
6. Agent generates execution plan (Runbook)
7. Upon execution, agent creates incremental PRs
8. Results and status updates flow back to dashboard
