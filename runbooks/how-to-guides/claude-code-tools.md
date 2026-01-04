# Claude Code tool permissions

Configure which Claude Code tools agents can use during planning and execution phases. This gives you control over agent capabilities and enforces security policies.

## Overview

Runbooks uses Claude Code to execute tasks. Claude Code has access to various tools like file operations, shell commands, and web searches. You can restrict these tools using allowlists and denylists.

**Allowlist**: Only listed tools are permitted. If set, unlisted tools are blocked.

**Denylist**: Listed tools are blocked. All other tools remain available.

## Configuration

Navigate to **Runbooks Settings > Claude Code Tools** to configure tool permissions.

You can set separate configurations for:
- **Planning phase**: When agents analyze code and create plans
- **Execution phase**: When agents implement changes

## Available tools

| Tool | Description |
|------|-------------|
| `Bash` | Execute shell commands |
| `Read` | Read file contents |
| `Write` | Create or overwrite files |
| `Edit` | Modify existing files |
| `MultiEdit` | Batch file edits |
| `Glob` | Find files by pattern |
| `Grep` | Search file contents |
| `Task` | Spawn sub-agents |
| `WebSearch` | Search the web |
| `WebFetch` | Fetch URL contents |
| `NotebookEdit` | Edit Jupyter notebooks |
| `TodoWrite` | Manage task lists |
| `KillShell` | Terminate running shells |
| `ExitPlanMode` | Exit planning mode |

## Tool patterns

### Basic tools

Specify tool names directly:

```
Read
Write
Grep
WebSearch
```

### Bash command patterns

Restrict Bash to specific commands using patterns:

```
Bash(npm run build)
Bash(npm run test:*)
Bash(pytest:*)
```

The `:*` suffix acts as a wildcard, matching any arguments after the prefix.

**Examples**:
- `Bash(npm run build)` - Only allows exact command `npm run build`
- `Bash(npm run:*)` - Allows `npm run build`, `npm run test`, `npm run lint`, etc.
- `Bash(pytest:*)` - Allows `pytest` with any arguments

### MCP tools

If you have MCP servers configured, their tools appear with the `mcp__` prefix:

```
mcp__github
mcp__github__create_issue
mcp__slack__post_message
```

**Format**: `mcp__<server_name>` or `mcp__<server_name>__<tool_name>`

Note: Wildcards are not supported for MCP tools. Each tool must be listed explicitly.

## Example configurations

### Restrict planning to read-only

Allow agents to explore but not modify during planning:

**Planning allowlist**:
```
Read
Grep
Glob
WebSearch
Task
```

### Restrict execution to specific commands

Limit what agents can execute:

**Execution allowlist**:
```
Read
Write
Edit
Bash(npm run build)
Bash(npm run test:*)
Bash(npm run lint)
```

### Block dangerous operations

Prevent specific actions:

**Execution denylist**:
```
Bash(rm -rf:*)
Bash(git push:*)
Bash(curl:*)
WebFetch
```

## Precedence

If both allowlist and denylist are set:
1. Allowlist is applied first (only listed tools are considered)
2. Denylist is applied second (removes tools from the allowed set)

## Default behavior

With no configuration:
- All standard Claude Code tools are available
- Git operations (`git commit`, `git push`) are blocked by the system
- Runbooks manages git operations automatically for consistent PR workflows
