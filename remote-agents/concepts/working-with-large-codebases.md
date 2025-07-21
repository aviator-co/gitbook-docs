# Working with large codebases

Handling enterprise-scale repositories requires specific strategies and optimizations.

## Code Analysis Strategy

### **Intelligent Searching**

* Agents download code to remote servers
* Use reference-based searching for efficiency
* Progressive analysis from entry points

### **Token Optimization**

* Focus on relevant code sections
* Incremental context building
* Caching of analysis results

## Performance Considerations

```
Repository Size    Recommended Approach
──────────────    ───────────────────
< 100K LOC        Standard analysis
100K-1M LOC       Targeted search patterns
> 1M LOC          Modular execution strategy
```

## Large Codebase Best Practices

1. **Divide and Conquer**
   * Break transformations into smaller Runbooks
   * Focus on specific modules or packages
   * Use incremental rollout strategy
2. **Context Management**
   * Carefully curate relevant documentation
   * Use MCP for external context
   * Limit scope to necessary files
3. **Resource Planning**
   * Monitor token usage trends
   * Scale agent containers as needed
   * Use caching effectively
