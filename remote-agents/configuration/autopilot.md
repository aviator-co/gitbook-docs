# Autopilot

Autopilot mode enables autonomous execution of high-confidence opportunities.

## Configuration

```yaml
autopilot:  enabled: true  auto_execute:    high_confidence: true    medium_confidence: false    low_confidence: false  constraints:    max_prs_per_day: 10    require_green_builds: true    blocked_paths:      - "*.sql"      - "migrations/*"  notifications:    on_success: "email"    on_failure: "slack"
```

## Safety Mechanisms

1. **Confidence Thresholds**
   * Only execute above configured confidence
   * Human review for uncertain changes
2. **Rate Limiting**
   * Maximum PRs per time period
   * Prevent overwhelming reviewers
3. **Rollback Triggers**
   * Automatic revert on build failures
   * Performance degradation detection

## Monitoring Autopilot

### **Dashboard Metrics**

* Opportunities processed
* Success/failure rates
* Average resolution time
* Token usage trends

### **Audit Trail**

* All automated actions logged
* Decision rationale captured
* Rollback history maintained
