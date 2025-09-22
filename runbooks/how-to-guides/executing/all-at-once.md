# All at once

Execute entire Runbook automatically with agents handling all steps.

## When to Use

* Well-tested Runbooks
* Low-risk transformations
* Time-sensitive changes
* High confidence in agent performance

## Execution Strategy

1. Agent validates entire plan
2. Creates execution schedule
3. Generates incremental PRs
4. Handles failures automatically
5. Reports final status
