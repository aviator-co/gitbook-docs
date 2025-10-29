# SSH Sandboxes Configuration Guide

[SSH Sandboxes](../concepts/ssh-sandboxes.md) allow you to run Claude Code runbooks on your own infrastructure instead of using Aviator’s managed cloud sandboxes. This gives you more control over the execution environment and allows you to work with private resources that aren’t accessible from the cloud.

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
4. **Working Directory**: A designated directory where Claude Code can create and modify files
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

### Best Practices

#### Security

1. **Use dedicated SSH keys**: Create a separate SSH key pair specifically for Aviator
2. **Limit user permissions**: Use a dedicated user account with minimal required permissions
3. **Restrict network access**: Configure firewall rules to allow connections only from Aviator’s IP ranges (`34.127.109.72`, `35.247.14.14`)
4. **Regular key rotation**: Periodically update your SSH private keys

#### Server Setup

1. **Working directory**: Ensure the working directory has sufficient disk space and proper permissions
2. **Dependencies**: Install any tools or dependencies your runbooks might need
3. **Monitoring**: Set up monitoring to track resource usage during runbook execution
4. **Backup**: Regularly backup important data, as runbooks may modify files

#### Performance

1. **Server resources**: Ensure adequate CPU, memory, and disk space for your runbooks
2. **Network latency**: Choose servers with low latency to Aviator’s infrastructure (we are hosted in US-Oregon region)
3. **Multiple instances**: Configure multiple sandbox instances for redundancy and load distribution

### Troubleshooting

#### Common Issues

**SSH Connection Failed**

* Verify hostname and port are correct
* Check if SSH service is running on the target server
* Ensure firewall allows connections from Aviator’s infrastructure
* Verify the SSH private key format and permissions

**Authentication Failed**

* Confirm the SSH private key matches the public key on the server
* Check username is correct
* Verify the user has SSH access permissions

**Working Directory Issues**

* Ensure the directory exists and is accessible
* Check that the SSH user has read/write permissions
* Verify sufficient disk space is available

**Runbook Execution Errors**

* Check server logs for detailed error messages
* Verify required dependencies are installed
* Ensure the working directory has proper permissions

#### Getting Help

If you encounter issues:

1. Check the server’s SSH logs (`/var/log/auth.log` or `/var/log/secure`)
2. Verify network connectivity from your local machine to the server
3. Test SSH connection manually with the same credentials
4. Contact Aviator support with relevant error messages and configuration details

### Security Considerations

* SSH private keys are encrypted at rest using industry-standard encryption
* Keys are never logged or exposed in plain text
* All SSH connections use secure authentication
* Consider using certificate-based authentication for enhanced security
* Regularly audit SSH access logs on your servers

### See also

* [ssh-sandboxes.md](../concepts/ssh-sandboxes.md "mention")
* [cloud-sandboxes.md](../concepts/cloud-sandboxes.md "mention")
