# MCP servers

Configure Model Context Protocol (MCP) servers to extend agent capabilities with custom tools and integrations.

## Overview

MCP servers provide additional tools that agents can use during Runbook execution. Common use cases include:
- Database access
- API integrations (GitHub, Slack, Jira)
- Custom internal tools
- File system operations on remote systems

## Configuration

Navigate to **Runbooks Settings > MCP Servers** to configure servers.

The configuration uses JSON format:

```json
{
  "mcpServers": {
    "server-name": {
      // server configuration
    }
  }
}
```

## Server types

### stdio

Local command-based servers that communicate via stdin/stdout.

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

**Required fields**:
- `command`: The executable to run

**Optional fields**:
- `type`: Set to `"stdio"` (default if omitted)
- `args`: Array of command arguments
- `env`: Environment variables (object of key-value strings)

### sse

Remote servers using Server-Sent Events transport.

```json
{
  "mcpServers": {
    "remote-tools": {
      "type": "sse",
      "url": "https://mcp.example.com/sse",
      "headers": {
        "Authorization": "Bearer ${API_TOKEN}"
      }
    }
  }
}
```

**Required fields**:
- `type`: Must be `"sse"`
- `url`: Server endpoint URL

**Optional fields**:
- `headers`: HTTP headers (object of key-value strings)

### http

Remote servers using HTTP transport.

```json
{
  "mcpServers": {
    "api-tools": {
      "type": "http",
      "url": "https://mcp.example.com/api",
      "headers": {
        "X-API-Key": "${API_KEY}"
      }
    }
  }
}
```

**Required fields**:
- `type`: Must be `"http"`
- `url`: Server endpoint URL

**Optional fields**:
- `headers`: HTTP headers (object of key-value strings)

## Using secrets

Reference secrets in your configuration using `${SECRET_NAME}` syntax:

```json
{
  "mcpServers": {
    "database": {
      "type": "stdio",
      "command": "mcp-postgres",
      "env": {
        "DATABASE_URL": "${DATABASE_URL}"
      }
    }
  }
}
```

Create secrets in **Runbooks Settings > Secrets**. Secrets are encrypted at rest and injected at runtime.

## Example configurations

### GitHub integration

```json
{
  "mcpServers": {
    "github": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "${GITHUB_TOKEN}"
      }
    }
  }
}
```

### Slack integration

```json
{
  "mcpServers": {
    "slack": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-slack"],
      "env": {
        "SLACK_BOT_TOKEN": "${SLACK_TOKEN}"
      }
    }
  }
}
```

### PostgreSQL access

```json
{
  "mcpServers": {
    "postgres": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-postgres"],
      "env": {
        "DATABASE_URL": "${DATABASE_URL}"
      }
    }
  }
}
```

### Multiple servers

```json
{
  "mcpServers": {
    "github": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "${GITHUB_TOKEN}"
      }
    },
    "filesystem": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/workspace"]
    }
  }
}
```

## Using MCP tools in Runbooks

Once configured, MCP tools appear with the `mcp__` prefix in Claude Code:

- `mcp__github__create_issue`
- `mcp__slack__post_message`
- `mcp__postgres__query`

You can control access to these tools using [Claude Code tool permissions](claude-code-tools.md).

## Restrictions

- The `sdk` server type is not available in user configurations
- Server names must be alphanumeric with hyphens and underscores only
- Each server name must be unique within your configuration
