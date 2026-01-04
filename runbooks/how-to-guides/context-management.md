# Context management

Context files provide persistent documentation that agents reference during planning and execution. They help agents understand your codebase structure, coding standards, and project-specific requirements.

## What are context files?

Context files are markdown documents attached to your Runbooks account. When agents plan or execute tasks, they can access these files to understand:

- Project architecture and conventions
- API patterns and data models
- Testing requirements
- Deployment procedures
- Team-specific coding standards

## Creating a context file

Navigate to **Runbooks Settings > Context Files** and click **Add Context File**.

Each context file requires:
- **Title**: Extracted automatically from the first `# Heading` in your markdown
- **Content**: Markdown document with your context information

### Structure

Use a clear heading as your title:

```markdown
# Authentication System

## Overview
Our app uses JWT-based authentication with refresh tokens.

## Key Files
- `src/auth/jwt.py` - Token generation and validation
- `src/auth/middleware.py` - Request authentication
- `src/models/user.py` - User model with password hashing

## Patterns
- All protected routes use the `@require_auth` decorator
- Tokens expire after 15 minutes
- Refresh tokens are stored in HTTP-only cookies
```

### Important files

Context files can reference important files in your codebase. The system extracts file paths from your markdown and uses them to prioritize context during agent planning.

Reference files using standard code formatting:
- Inline: `` `src/auth/jwt.py` ``
- Code blocks with file paths

## Using context files

Context files are automatically available to agents. You don't need to explicitly attach them to each Runbook.

When creating a Runbook, agents will:
1. Search relevant context files based on the task
2. Use file references to understand code structure
3. Follow documented patterns and conventions

## Updating context files

Edit context files from **Runbooks Settings > Context Files**. Changes apply immediately to new Runbook sessions.

For existing sessions, agents will pick up changes when:
- You start a new planning phase
- The agent re-reads context during execution

## Best practices

**Keep files focused**: One context file per major system or domain. Avoid monolithic documents.

**Document patterns, not code**: Explain conventions and "why" rather than duplicating code.

**Include file paths**: Reference actual files so agents can locate implementations.

**Update regularly**: Keep context files in sync with your codebase. Outdated context leads to incorrect suggestions.
