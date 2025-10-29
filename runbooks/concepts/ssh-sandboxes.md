# SSH sandboxes

SSH sandboxes provide a self-managed execution environment for Runbooks, allowing organizations to run Claude Code on their own infrastructure instead of using Aviator's managed cloud sandboxes. This approach offers enhanced security, control, and customization for enterprise environments.

### Architecture overview

SSH sandboxes operate as remote Linux environments that Aviator connects to via SSH transport. Each sandbox maintains a working directory where Git repositories are cloned and managed. The system supports multiple sandbox instances per account for load balancing and redundancy.

The implementation uses SSH key-based authentication with encrypted private key storage. Connection management includes pooling, locking mechanisms to prevent conflicts, and automatic retry logic for robust operation.

### Configuration requirements

#### Account-level setup

Organizations configure SSH sandboxes through the Runbooks settings interface. The configuration requires selecting SSH as the sandbox type and providing an SSH private key for authentication.

Private keys support OpenSSH, RSA, and EC formats. Keys are encrypted using Fernet encryption before storage and automatically decrypted during connection establishment.

<figure><img src="../../.gitbook/assets/Screenshot 2025-10-29 at 3.34.06 PM.png" alt="" width="563"><figcaption></figcaption></figure>

#### SSH sandbox instances

Individual sandbox instances require configuration of hostname or IP address, SSH port (default 22), username (default root), and working directory path for repository operations.

Each sandbox can be individually enabled or disabled through the active status flag. The system enforces unique hostnames per account to prevent configuration conflicts.

#### Infrastructure requirements

SSH sandboxes require Linux servers with specific software installations: Git with LFS support, Node.js for npm packages, Python with uv package manager, and Claude Code CLI. It also optionally can include build tools including curl and build-essential.

### Managing Sandboxes

To learn more about managing the Sandboxes, checkout [ssh-sandboxes-configuration-guide.md](../how-to-guides/ssh-sandboxes-configuration-guide.md "mention")

### Execution workflow

#### Sandbox selection and initialization

When executing Runbooks with SSH sandboxes configured, the system automatically selects an available sandbox instance from the pool. Redis-based locking prevents concurrent usage of the same sandbox instance.

Repository cloning occurs automatically using authenticated Git URLs from the GitHub integration. The system detects existing repositories to avoid unnecessary re-cloning and supports Git submodules and LFS.

#### Command execution

Claude Code commands execute remotely via SSH transport with JSON message streaming between Aviator and the remote instance. Environment variables are injected during execution, and stdout/stderr streams are captured and forwarded to the Runbooks interface.

Working directories are created uniquely for concurrent executions, with automatic cleanup of temporary files after completion. Existing repositories persist across sessions for improved performance.

#### Resource management

Connection pooling manages SSH connections efficiently with automatic reuse where possible. Load balancing distributes workload across available sandbox instances when multiple sandboxes are configured.

The system implements comprehensive cleanup procedures, removing temporary directories and releasing locks after execution completion.

### Authentication and security

#### SSH key management

SSH authentication relies on public-private key pairs configured between Aviator and the sandbox instances. Organizations generate key pairs using their preferred format and configure the public key in the sandbox's authorized\_keys file.

Private keys are stored encrypted in Aviator's database and undergo automatic validation during configuration. The system supports key rotation through the settings interface.

#### Git repository access

Git authentication leverages existing GitHub App permissions and authenticated URLs. Sandbox environments receive automatic credential configuration enabling access to private repositories based on the organization's GitHub integration settings.

### Performance considerations

#### Connection optimization

The SSH transport implementation includes connection pooling to minimize establishment overhead. Repository caching reduces cloning time for subsequent executions of the same codebase.

Multiple sandbox instances enable parallel execution when workload demands exceed single-instance capacity. Lock coordination ensures safe concurrent operations without resource conflicts.

#### Scaling patterns

Organizations can provision additional sandbox instances to handle increased execution volume. The system automatically distributes load across available instances without requiring manual intervention.

Sandbox instances can be scaled independently based on usage patterns, with the ability to temporarily disable instances for maintenance without disrupting service.

### Integration benefits

#### Enterprise compliance

SSH sandboxes enable organizations to maintain code execution within their own infrastructure boundaries, supporting compliance requirements that restrict external code processing.

Security policies can be implemented at the infrastructure level, including network isolation, monitoring, and access controls beyond what cloud sandboxes provide.

#### Customization capabilities

Organizations can install custom tools, configure specific environment settings, and implement specialized build processes within their sandbox environments.

Development team preferences for specific tool versions, security configurations, or monitoring integrations can be accommodated through direct sandbox customization.

#### Cost management

SSH sandboxes eliminate per-execution charges associated with cloud sandbox usage, particularly beneficial for organizations with high Runbooks usage volumes.

Infrastructure costs become predictable through fixed sandbox provisioning rather than variable usage-based pricing models.

### See also

* [ssh-sandboxes-configuration-guide.md](../how-to-guides/ssh-sandboxes-configuration-guide.md "mention")
* [cloud-sandboxes.md](cloud-sandboxes.md "mention")
