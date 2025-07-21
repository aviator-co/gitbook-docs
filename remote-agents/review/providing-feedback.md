# Providing feedback

Feedback loops enable continuous improvement of agent performance.

## Feedback Channels

1. **Build/Test Results**
   * Automatic integration with CI/CD
   * Agent analyzes failures
   * Iterative fixes applied
2. **PR Comments**
   * Human review feedback
   * Specific change requests
   * General guidance
3. **Chat Interface**
   * Direct agent interaction
   * Clarification requests
   * Strategy adjustments

## Feedback Processing

```
Feedback Type        Agent Response
─────────────        ──────────────
Build Failure        Analyze logs, fix code
Test Failure         Debug issue, update tests
Review Comment       Interpret, implement change
General Guidance     Update approach, retry
```

## Global Learning

Selected feedback can be captured as global context:

* Common error patterns
* Team preferences
* Domain-specific knowledge
* Best practices
