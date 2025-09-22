# Single step

Execute Runbook steps individually with manual verification between each step.

## When to Use

* Complex transformations with high risk
* Learning how agents interpret instructions
* Debugging failed executions
* Regulatory compliance requirements

## Execution Flow

```
┌──────────┐     ┌──────────┐     ┌──────────┐     ┌──────────┐
│Execute   │────▶│ Verify   │────▶│ Approve  │────▶│   Next   │
│  Step    │     │ Changes  │     │    PR    │     │   Step   │
└──────────┘     └──────────┘     └──────────┘     └──────────┘
```

## Benefits

* Fine-grained control
* Opportunity to adjust approach
* Lower risk of cascading failures
* Better learning opportunity

***
