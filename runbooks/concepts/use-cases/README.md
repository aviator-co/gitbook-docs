---
description: Take a look at some common use cases with examples.
---

# Use Cases

Runbooks are meant to solve most of the use cases that involve improving and modifying an existing codebase, but not everything is worth using Runbooks for.

### When to use Runbooks

Runbooks are great for most well defined or well scoped tasks. It is meant to absorb business context and get smarter over time. Some examples include:

* [Product backlog](product-backlog.md)
* [Refactoring](code-refactoring.md)
* [Bug fixes](bug-fixes.md)
* [Improving build times](improve-build-times.md)
* [Flaky test resolution](flaky-test-resolution.md)
* [Code migration](code-migrations.md)
* [Improving readability](improving-readability.md)
* [Improving code coverage](improving-code-coverage.md)

### When not to use Runbooks

Runbooks are not great for brainstorming ideas or working on greenfield projects. If requires a lot of back-and-forth with your agents, you might be better off using Claude code or similar coding tools in your terminal.

### Creating templates

When you use Runbooks for a specific use case that occurs often (e.g. a bug fix or a flaky test resolution), create templates. [Template](../templates.md) can be created from any new or existing Runbook. These are great ways to share your process with the team and enhance collective development.

### Getting Started

1. **Choose a simple feature**: Start with a well-defined, isolated feature
2. **Write clear requirements**: Provide detailed specifications and examples
3. **Review and refine**: Work with Runbooks to refine the implementation plan
4. **Execute step by step**: Run the implementation in stages with review points
5. **Learn and iterate**: Use the experience to improve future automation

