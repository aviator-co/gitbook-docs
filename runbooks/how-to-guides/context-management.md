# Context management

This guide covers how to view, create, and manage learnings and context files for your Runbooks account.

For an overview of how context works, see [Context and Learnings](../concepts/context-and-learnings.md).

## Managing learnings

View and manage learnings from the **Context** tab in your Runbooks dashboard.

### Browsing learnings

The Context tab shows all learnings captured across your account:

- **Search and filter** — Find learnings by keyword, framework, or file pattern
- **Sort by relevance** — See most frequently used or most recent learnings first
- **View details** — Click any learning to see its full pattern, solution, and usage history
- **See source sessions** — Track which Runbook sessions contributed each learning

### Creating custom learnings

You can manually add learnings to share project knowledge with your team:

1. Go to the **Context** tab
2. Click **Add Learning**
3. Fill in the learning details:
   - **Pattern** — Describe the problem or situation (e.g., "pytest fails with import errors in monorepo")
   - **Solution** — What works to resolve it (e.g., "Add conftest.py with sys.path configuration")
   - **Applies to** — Optional filters for files, frameworks, or commands
4. Click **Save**

Custom learnings are treated the same as auto-captured ones — they'll surface in future Runbook sessions when relevant.

### Editing and removing learnings

- **Edit** — Update a learning's pattern, solution, or applicability filters
- **Delete** — Remove learnings that are no longer relevant or were captured incorrectly

---

## Managing context files

Context files are markdown documents that describe your project architecture and conventions.

### Creating a context file

1. Navigate to **Runbooks Settings > Context Files**
2. Click **Add Context File**
3. Write your markdown content with a `# Heading` as the title

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

### Referencing important files

Reference files in your codebase using code formatting. The system extracts these paths and uses them to prioritize context during planning:

- Inline: `` `src/auth/jwt.py` ``
- In lists or code blocks

### Updating context files

Edit context files from **Runbooks Settings > Context Files**. Changes apply immediately to new Runbook sessions.

For existing sessions, agents pick up changes when:
- You start a new planning phase
- The agent re-reads context during execution

---

## Best practices

**Keep context files focused** — One file per major system or domain. Avoid monolithic documents.

**Document patterns, not code** — Explain conventions and "why" rather than duplicating code.

**Include file paths** — Reference actual files so agents can locate implementations.

**Let learnings accumulate** — The more you use Runbooks, the smarter they become.

**Review learnings periodically** — Check the Context tab to remove outdated patterns or refine solutions.

---

## FAQ

**Can I see what learnings have been captured?**

Yes. Browse all learnings in the Context tab. You can also see session-specific learnings in the details section of each Runbook session.

**Do learnings slow down my workflow?**

No. Learning capture happens in the background and doesn't block your work.

**Can I disable learning capture?**

Contact support if you need to disable learning capture for your account.

**Are learnings used in all Runbook sessions?**

Yes, relevant learnings are automatically retrieved for all Runbook sessions in your account.
