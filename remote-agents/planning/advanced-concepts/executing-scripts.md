# Executing scripts

Agents can execute custom scripts as part of transformations.

**Script Integration**

```
Step: "Run Migration Script"
Type: "script_execution"
Parameters:
  - script: "migrate_database.sh"
  - environment: "staging"
  - validation: "check_migration_status.py"
```

**Supported Operations**

* Build system commands
* Testing frameworks
* Linting and formatting
* Custom analysis tools
