# Architecture Overview

The Remote Agentic Environment consists of several interconnected components that work together to enable autonomous code transformations.

#### Core Components

**API Gateway**

* Handles user authentication and request routing
* Manages session state and security
* Interfaces with the web dashboard

**Orchestration Layer**

* Manages agent container lifecycle
* Distributes tasks across available workers
* Monitors execution progress and health

**Agent Containers**

* Run Aviator agents for code analysis and transformation
* Minimum requirements: 8GB RAM, 4 vCPU
* Communicate with control layer and GitHub

**Web Dashboard**

* Chat interface for planning and executing tasks
* Runbook search and management
* Context management with MCP and documentation support

#### Data Flow

1. User submits task through dashboard
2. API Gateway authenticates and forwards to orchestration
3. Orchestration layer assigns task to available agent container
4. Agent analyzes code via GitHub API/Git protocols
5. Agent generates execution plan (Runbook)
6. Upon execution, agent creates incremental PRs
7. Results and status updates flow back to dashboard
