# PR Lifecycle

Understanding how agents manage pull requests throughout their lifecycle.

## PR Creation

Agents follow a specific naming convention that makes it easy to identify changes made by the agent.

```
[Runbook-ID] Step X: <descriptive title>
```

## **PR Structure**

* Clear description of changes
* Link to parent Runbook
* Test results summary
* Review checklist

## PR Management

### **States**

```
Draft ──▶ Ready ──▶ In Review ──▶ Approved ──▶ Merged
  │         │           │            │
  └─────────┴───────────┴────────────┴──▶ Requires Changes
```

### **Agent Actions**

* Monitor CI/CD status
* Respond to review comments
* Update based on feedback
* Resolve conflicts
* Manage dependencies

### Stacked PR Workflow

1. Base PR created first
2. Dependent PRs reference parent
3. Sequential merging strategy
4. Automatic rebase handling
5. Conflict resolution
