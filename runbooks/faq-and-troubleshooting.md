# FAQ & Troubleshooting

#### Frequently Asked Questions

**Q: What programming languages are supported?**

A: The system is language and framework agnostic. Agents can work with any codebase accessible via GitHub.

**Q: What agents are supported?**

A: Currently Runbooks supports Claude code agents, the support for Gemini and Codex is coming soon.

**Q: Are there repository size limitations?**

A: There are no hard limits on repository size, though larger codebases may consume more LLM tokens.

**Q: How do agents handle build failures?**

A: Agents automatically analyze build and test results, then iteratively recreate changes to fix issues.

**Q: Can I use my own LLM API keys?**

A: Yes, you can provide your own Claude API keys. Aviator also support Bedrock.

#### Common Issues

**Agent Connection Failures**

* Verify GitHub App permissions are correctly configured
* Check network connectivity from agent containers
* Ensure API gateway is accessible

**High Token Usage**

* Review context management settings
* Consider breaking large tasks into smaller Runbooks
* Optimize search patterns for large codebases

**PR Creation Errors**

* Confirm write permissions for target repository
* Check branch protection rules
* Verify agent has latest repository state
