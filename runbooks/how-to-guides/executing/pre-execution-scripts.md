# Pre-Execution Scripts

### Overview

Pre-execution scripts allow you to run custom setup commands automatically before each Runbook step is executed. This feature is useful for environment configuration, dependency installation, cache warming, or any other preparatory tasks that your code changes require.

### How It Works

When you execute a Runbook step, Aviator automatically checks for a pre-execution script at a specific location in your repository. If found, the script is executed in the sandbox environment before Claude Code begins working on the step.

#### Execution Flow

1. **Step begins** - A Runbook step is queued for execution
2. **Sandbox setup** - The appropriate branch is checked out in the sandbox
3. **Context injection** - Any existing context files are loaded
4. **Pre-execution script** - If `.aviator/scripts/pre-execution.sh` exists, it runs
5. **Step execution** - Claude Code proceeds with the actual step implementation
6. **PR creation** - Changes are committed and pushed to a pull request

### Configuration

#### Script Location

Place your pre-execution script at the following path in your repository:

```
.aviator/scripts/pre-execution.sh
```

This path is checked at the start of every step execution. If the file doesn't exist, execution proceeds normally without any errors.

#### Script Requirements

* **Format**: Bash shell script
* **Permissions**: The script will be automatically made executable (`chmod +x`)
* **Execution**: Runs with `bash pre-execution.sh`
* **Working Directory**: Executes in the root of your repository

### Examples

#### Example 1: Install Dependencies

```bash
#!/bin/bash
# .aviator/scripts/pre-execution.sh

echo "Installing npm dependencies..."
npm install

echo "Building project..."
npm run build

exit 0
```

#### Example 2: Environment Setup

```bash
#!/bin/bash
# .aviator/scripts/pre-execution.sh

echo "Setting up environment..."

# Copy environment configuration
cp .env.example .env

# Install Python dependencies
pip install -r requirements.txt

# Run database migrations
python manage.py migrate

echo "Environment setup complete!"
exit 0
```

#### Example 3: Conditional Setup Based on Files

```bash
#!/bin/bash
# .aviator/scripts/pre-execution.sh

echo "Running pre-execution setup..."

# Check if package.json exists
if [ -f "package.json" ]; then
    echo "Found package.json, installing Node dependencies..."
    npm install
fi

# Check if requirements.txt exists
if [ -f "requirements.txt" ]; then
    echo "Found requirements.txt, installing Python dependencies..."
    pip install -r requirements.txt
fi

exit 0
```

#### Example 4: Cache Warming

```bash
#!/bin/bash
# .aviator/scripts/pre-execution.sh

echo "Warming up caches..."

# Pre-compile assets
npm run build:assets

# Warm up application cache
curl -s http://localhost:3000/api/warmup > /dev/null

echo "Cache warming complete!"
exit 0
```

### Output and Feedback

#### Successful Execution

When your pre-execution script runs successfully (exits with code 0), you'll see:

* ✅ A success message in the Runbook chat
* The script's stdout output (first 2000 characters)
* Indication that step execution is proceeding

**Example output:**

```
✅ Pre-execution script completed successfully

Output:
Installing npm dependencies...
added 234 packages in 5s
Building project...
Build completed successfully
```

#### Failed Execution

If your pre-execution script fails (exits with non-zero code), you'll see:

* ⚠️ A warning message with the exit code
* Both stdout and stderr output (first 1000 characters each)
* Step execution continues despite the failure

**Example output:**

```
⚠️ Pre-execution script failed with exit code 1

Stdout:
Installing npm dependencies...

Stderr:
npm ERR! missing package.json
npm ERR! enoent ENOENT: no such file or directory
```

**Important**: A failed pre-execution script does not stop step execution. The step will continue, but may fail later if required setup was incomplete.

### Best Practices

#### 1. Keep Scripts Idempotent

Your script should be safe to run multiple times:

```bash
#!/bin/bash

# Good: Check before installing
if [ ! -d "node_modules" ]; then
    npm install
fi

# Good: Use --force-reinstall only when needed
pip install -r requirements.txt --quiet
```

#### 2. Provide Clear Output

Help users understand what's happening:

```bash
#!/bin/bash

echo "=== Pre-execution Setup ==="
echo "1. Installing dependencies..."
npm install

echo "2. Running database migrations..."
python manage.py migrate

echo "3. Setup complete!"
```

#### 3. Handle Errors Gracefully

Exit with appropriate codes and messages:

```bash
#!/bin/bash

set -e  # Exit on first error

echo "Installing dependencies..."
if ! npm install; then
    echo "ERROR: Failed to install npm dependencies"
    exit 1
fi

echo "Success!"
exit 0
```

#### 4. Keep Execution Time Reasonable

The script runs on every step, so keep it fast:

* Cache dependencies when possible
* Avoid unnecessary rebuilds
* Skip steps that are already complete

```bash
#!/bin/bash

# Check if already built
if [ -f ".build_cache" ]; then
    echo "Build cache found, skipping rebuild"
    exit 0
fi

echo "Building project..."
npm run build
touch .build_cache
```

#### 5. Use Environment Variables

Configure behavior without code changes:

```bash
#!/bin/bash

# Allow disabling via environment variable
if [ "$SKIP_PRE_EXECUTION" = "true" ]; then
    echo "Pre-execution skipped (SKIP_PRE_EXECUTION=true)"
    exit 0
fi

echo "Running setup..."
npm install
```

### Troubleshooting

#### Script Not Running

If your pre-execution script isn't being executed:

1. **Check the path**: Ensure the file is at `.aviator/scripts/pre-execution.sh`
2. **Check the branch**: The script must exist in the branch being checked out
3. **Check file type**: Ensure it's a regular file, not a symlink
4. **Commit the script**: The file must be committed to the repository

#### Script Fails Silently

If your script fails without clear output:

1. Add `set -x` at the top to enable debug output
2. Add explicit error messages with `echo`
3. Ensure stdout and stderr are not redirected to `/dev/null`
4. Check for permission issues with file access

```bash
#!/bin/bash

set -x  # Enable debug mode
set -e  # Exit on error

echo "Starting pre-execution..."
# Your commands here
```

#### Slow Execution

If your pre-execution script takes too long:

1. Profile which commands are slow
2. Add caching for expensive operations
3. Consider if all steps are necessary
4. Use parallel execution when possible

```bash
#!/bin/bash

# Run independent tasks in parallel
npm install &
pip install -r requirements.txt &
wait

echo "All dependencies installed"
```

### Limitations

* **Timeout**: Scripts should complete within a reasonable timeframe. Timeout is 120 seconds.
* **One script per repository**: Only `.aviator/scripts/pre-execution.sh` is checked
* **Execution environment**: Runs in the sandbox environment with the checked-out code
* **No step-specific scripts**: The same script runs for all steps
* **No conditional skipping**: The script runs if it exists, regardless of step content

### Security Considerations

* Scripts run with the same permissions as Runbook step execution
* Be cautious with sensitive data in script output (it appears in chat)
* Avoid downloading untrusted dependencies or executing remote code
* Review scripts in PRs before merging

### FAQ

**Q: Does the pre-execution script run for every step?**

A: Yes, if the script exists, it runs before every Runbook step execution.

**Q: What happens if the script fails?**

A: The failure is logged and shown to the user, but step execution continues. The step may fail later if required setup was incomplete.

**Q: Can I have different scripts for different steps?**

A: Not currently. The same `.aviator/scripts/pre-execution.sh` runs for all steps.

**Q: Can I use other scripting languages?**

A: The script is executed with `bash`, but you can invoke other interpreters:

```bash
#!/bin/bash
python3 my_setup_script.py
node my_setup_script.js
```

**Q: Will the script run in one-shot mode?**

A: Yes, the pre-execution script runs in both regular and one-shot Runbook execution modes.

**Q: Can I see detailed logs of what the script did?**

A: The script's stdout is shown in the chat (first 2000 characters). For full logs, the script runs in the same sandbox where Claude Code operates.

### Related Documentation

* [Single step](single-step.md)
* [Cloud Sandbox Configuration](../cloud-sandboxes.md)
* [Custom Personas](../persona-management.md)
