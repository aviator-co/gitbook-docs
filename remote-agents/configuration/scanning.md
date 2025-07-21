# Scanning

Configure automatic repository scanning for opportunity discovery.

## Scan Configuration

```yaml
scanning:  schedule: "0 2 * * *"  # Daily at 2 AM  repositories:    - owner: "myorg"      name: "backend-api"      branches: ["main", "develop"]    - owner: "myorg"      name: "frontend-app"      branches: ["main"]  opportunity_types:    - code_quality    - security    - performance    - test_coverage  confidence_threshold: "medium"
```

## Scan Types

**Comprehensive Scan**

* Full repository analysis
* All opportunity types
* Detailed reporting

## **Targeted Scan**

* Specific paths or modules
* Selected opportunity types
* Faster execution

## **Incremental Scan**

* Changes since last scan
* Efficient for large repos
* Continuous improvement

## Scan Results

Results available through:

* Web dashboard
* Email notifications
* API endpoints
* Webhook integrations
