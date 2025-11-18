# SSH Sandboxes setup for onprem

Onprem setup is very similar to the [cloud SSH sandbox setup](ssh-sandboxes-configuration-guide.md), only a few things differ.

### Overview

SSH Sandboxes connect to your own servers or virtual machines where Claude Code can execute runbook steps. This is useful when:

* You need access to private internal resources
* You have specific security or compliance requirements
* You want to control the execution environment and dependencies
* You need to work with on-premises infrastructure

### Prerequisites

Before configuring SSH sandboxes, ensure you have:

1. **SSH Server Access**: A Linux/Unix server with SSH access
2. **SSH Key Pair**: An SSH private key for authentication
3. **Claude Code**: Claude Code CLI is installed on this sandbox
4. **Working Directory**: A designated directory where Claude Code can create and modify files. This is the root level directory that will host GitHub repositories.
5. **Network Access**: The server should be reachable from Aviator’s infrastructure

### Configuration Steps

#### 1. Choose Sandbox Type

1. Navigate to **Runbooks** → **Config** → **Sandbox**
2. Select **SSH Sandbox (Self-managed)** from the sandbox type options
3. You’ll see requirements and configuration options appear

#### 2. Configure SSH Private Key

When you select SSH sandbox, you’ll need to provide an SSH private key:

1. **If no key is configured**: A textarea will appear for you to paste your SSH private key
2. **If a key is already stored**: You’ll see a “SSH private key is securely stored” message with an option to update it

**Supported Key Formats:**

* OpenSSH format (recommended)
* RSA keys
* EC (Elliptic Curve) keys

**Example OpenSSH key format:**

```
-----BEGIN OPENSSH PRIVATE KEY-----
b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAAA...
-----END OPENSSH PRIVATE KEY-----
```

#### 3. Set Up SSH Sandbox Instances

After configuring your SSH key, you need to register your sandbox instances using the REST API:

```bash
# Add a new SSH sandbox instance
curl -X POST <https://api.aviator.co/api/v1/sandbox/> \
  -H "Authorization: Bearer YOUR_API_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
      "hostname": "your-server.example.com",
      "port": 22,
      "username": "ubuntu",
      "working_dir": "/home/ubuntu/runbooks",
      "active": true
  }'
```

**Required Fields:**

* `hostname`: Server hostname or IP address
* `port`: SSH port (usually 22)
* `username`: SSH username for authentication
* `working_dir`: Absolute path where Claude Code can work
* `active`: Whether this sandbox is available for use

### Managing SSH Sandbox Instances

#### List Sandbox Instances

```bash
curl -X GET <https://api.aviator.co/api/v1/sandbox/> \
  -H "Authorization: Bearer YOUR_API_TOKEN"
```

#### Update a Sandbox Instance

```bash
curl -X PUT <https://api.aviator.co/api/v1/sandbox/INSTANCE_ID> \
  -H "Authorization: Bearer YOUR_API_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
      "hostname": "updated-server.example.com",
      "port": 2222,
      "username": "deploy",
      "working_dir": "/opt/runbooks",
      "active": true
  }'
```

#### Delete a Sandbox Instance

```bash
curl -X DELETE <https://api.aviator.co/api/v1/sandbox/INSTANCE_ID> \
  -H "Authorization: Bearer YOUR_API_TOKEN"
```

### Using Sidecar container for sandboxes

In a kubernetes self-hosted configuration, it's possible to use Sidecar containers within the agent-worker pod such that each agent-worker pod is running a SSH sandbox alongside the primary agent-worker container.

To configure such container, specify the following environment variables on your pods:

* `SSH_SANDBOX_SIDECAR_URL`: This would likely be `localhost` but it can be different depending on your setup. Also include the port if applicable, defaults to `22`, example: `localhost:5454`.
* `SSH_SANDBOX_SIDECAR_ROOT_DIR`: This is same as `working_dir` in the API above.

When using these environment variables, agent-workers will ignore all Sandboxes specified via the API. It will assume each worker has it's own sandbox.

This approach could be useful to incorporate ephemeral sandboxes within your kubernetes cluster running Aviator server, and be able to scale up or scale down the instances.

### Best Practices

#### Security

1. **Use dedicated SSH keys**: Create a separate SSH key pair specifically for Aviator
2. **Limit user permissions**: Use a dedicated user account with minimal required permissions
3. **Restrict network access**: Configure firewall rules to restrict ingress connections only from the Aviator's background worker instances (the main onprem server).
4. **Regular key rotation**: Periodically update your SSH private keys

#### Preloading instances

1. **Working directory**: Ensure the working directory has sufficient disk space and proper permissions. You can also preload the repositories in this directory for faster load time. For instance, if you specific working directory as: `/home/ubuntu/dev/` then, Aviator will search or create repositories in `/home/ubuntu/dev/<repo-name>/`. If you preload the repository here, Aviator will reuse it and fetch only required branch. This can help save load time.
2. **Dependencies**: Install any tools or dependencies your runbooks might need
3. **Monitoring**: Set up monitoring to track resource usage during runbook execution
4. **Backup**: Regularly backup important data, as runbooks may modify files

#### Performance

1. **Server resources**: Ensure adequate CPU, memory, and disk space for your runbooks
2. **Network latency**: Choose servers with low latency to Aviator’s infrastructure (we are hosted in US-Oregon region)
3. **Multiple instances**: Configure multiple sandbox instances for redundancy and load distribution

### Troubleshooting
