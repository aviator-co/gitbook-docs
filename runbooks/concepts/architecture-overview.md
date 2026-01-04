# Architecture Overview

Runbooks consists of several components that work together to enable collaborative AI-driven code changes.

<figure><img src="../../.gitbook/assets/Aviator Runbooks overview.png" alt=""><figcaption></figcaption></figure>

## Core Components

### Web Dashboard

The primary interface for interacting with Runbooks:

- **Chat interface**: Conversational UI for describing tasks and reviewing plans
- **Runbook editor**: View and modify generated execution steps
- **Session management**: Create, clone, and manage runbook sessions
- **Collaboration**: Invite team members, assign steps, share sessions
- **Configuration**: Manage personas, context files, MCP servers, and tool permissions

### API Layer

GraphQL API that handles:

- Authentication via GitHub OAuth, SAML, or API tokens
- Session state and user permissions
- Routing requests to background workers
- Real-time updates to the dashboard via WebSocket

### Orchestration Layer

Background task queues manage the execution lifecycle:

- **Task distribution**: Distributes work across available workers
- **Sandbox lifecycle**: Creates, monitors, and cleans up sandbox instances
- **Concurrency control**: Limits concurrent sandboxes per account
- **Retry logic**: Handles transient failures with exponential backoff

### Sandboxes

Isolated execution environments where Claude Code runs. All LLM interactions happen exclusively within sandboxes—your code and prompts never leave the sandbox boundary, preventing any possibility of prompt injection or data exfiltration from affecting your main systems.

**Cloud sandboxes**:
- Managed containers with pre-configured environments
- Auto-pause after inactivity, resume on demand
- Custom templates via Dockerfile
- Configurable timeout (1-240 minutes)

**SSH sandboxes**:
- Self-hosted Linux servers you control
- SSH key-based authentication
- Connection pooling and load balancing across instances
- Repository caching for faster subsequent runs

Both sandbox types:
- Clone your repository via authenticated Git URLs
- Run Claude Code with your configured permissions
- Execute pre-execution scripts (`.aviator/scripts/pre-execution.sh`)
- Stream output back to the dashboard in real-time via custom transport layers

## Session Management

Sessions are the core unit of work in Runbooks. Each session maintains:

**Conversation history**: All messages between users and agents are preserved. When you resume a session, Claude Code receives the full context of previous interactions, enabling coherent multi-turn conversations across days or weeks.

**Execution state**: Sessions track which steps are pending, in progress, or completed. You can pause execution, switch to a different step, or re-run failed steps without losing context.

**Collaborator access**: Multiple team members can join a session. Everyone sees the same conversation, can send messages, and trigger execution. This enables real-time collaboration where senior engineers can guide juniors through complex tasks.

**Branch and PR tracking**: Sessions maintain links to working branches and pull requests created during execution. When you provide feedback on a PR, the session context helps Claude Code understand what was attempted and what needs to change.

**Resume capability**: Claude Code sessions can be paused and resumed. The system preserves the agent's internal state, so resuming a session continues exactly where it left off rather than starting fresh.

## Tool Permissions

Control what Claude Code can do during planning and execution phases separately.

### Allowlists and Denylists

**Allowlist**: Only listed tools are permitted. Unlisted tools are blocked.

**Denylist**: Listed tools are blocked. All other tools remain available.

You can configure different permissions for each phase:
- **Planning phase**: Typically read-only tools for code analysis
- **Execution phase**: Write access for implementing changes

### Tool Patterns

**Basic tools**: `Read`, `Write`, `Edit`, `Grep`, `Glob`, `WebSearch`

**Bash commands with wildcards**:
```
Bash(npm run build)       # Exact command only
Bash(npm run:*)           # Any npm run subcommand
Bash(pytest:*)            # pytest with any arguments
```

**MCP tools**: `mcp__servername` or `mcp__servername__toolname`

### System Restrictions

Certain operations are always blocked regardless of configuration:
- Git commit, push, and other write operations (Runbooks manages git automatically)
- This ensures consistent commit messages, proper attribution, and controlled PR workflows

## MCP Servers

Extend Claude Code's capabilities with custom tool integrations via the Model Context Protocol.

### Configuration

MCP servers are configured per-account in JSON format:

```json
{
  "mcpServers": {
    "github": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_TOKEN": "${GITHUB_TOKEN}"
      }
    }
  }
}
```

### Server Types

**stdio**: Local command-based servers. Requires `command`, optional `args` and `env`.

**sse/http**: Remote servers. Requires `url`, optional `headers`.

### Using MCP Tools

Once configured, MCP tools are automatically available to Claude Code with the `mcp__` prefix:
- `mcp__github` - All tools from the github server
- `mcp__github__create_issue` - Specific tool

You can control access to MCP tools using the same allowlist/denylist mechanism as built-in tools.

### Secrets

Reference secrets in MCP configuration using `${SECRET_NAME}` syntax. Secrets are:
- Encrypted at rest
- Injected at runtime into the sandbox
- Never exposed in logs or UI after creation

## Execution Flow

### Planning Phase

1. User submits task description via chat
2. Background worker acquires a sandbox
3. Sandbox clones repository and checks out target branch
4. Claude Code receives system prompt (persona + context files) and user prompt
5. Agent analyzes codebase with planning-phase tool permissions
6. Generates step-by-step execution plan
7. Plan streams back to dashboard for review

### Execution Phase

1. User approves plan or triggers step execution
2. Background worker checks out appropriate branch
3. Runs pre-execution script if present (`.aviator/scripts/pre-execution.sh`)
4. Claude Code implements the step with execution-phase permissions
5. On completion:
   - Commits changes with structured message
   - Creates or updates pull request
   - Links PR to the step
6. Results stream back to dashboard
7. User can provide feedback, triggering iteration

### Feedback Loop

When you comment on a PR or provide feedback in the chat:
1. Claude Code receives context of previous work plus your feedback
2. Generates updated implementation
3. Pushes new commits to the existing PR
4. CI runs again, and the cycle continues until you're satisfied

## GitHub Integration

### Repository Access

- Authenticated via GitHub App installation
- Supports private repositories within the installation scope
- Git credentials refreshed automatically in sandboxes

### Pull Request Workflow

- PRs created per-step or per-runbook (configurable)
- Draft PRs for work-in-progress
- Automatic undraft when step completes
- PR body includes step description and runbook context
- Co-authored-by tags for attribution

### Branch Management

- Working branches created automatically
- Target branch configurable per session
- Clean state before each execution (uncommitted changes discarded)

## Integrations

### Slack

- **One-shot mode**: Execute simple tasks directly from Slack threads
- **Notifications**: Step completion, PR creation, failures
- **Links**: Deep links back to dashboard for full context

### Context Files

Persistent knowledge that helps agents understand your codebase:
- Markdown documents stored per-account
- Automatically injected into Claude Code prompts
- Reference important files for prioritized context
- See [Context management](../how-to-guides/context-management.md) for details
