# Persona management

## **Using Personas**

By default Aviator will choose the default personas for planning and execution. To switch A custom persona can be selected directly from the Rubook chat interface when interacting with the agents. To switch the persona, select the “Switch persona” from the actions menu on the top right.

## Creating Personas

Personas can be managed from the Runbook Settings. [Personas](../concepts/personas.md) are system prompts that are passed over to LLM agents to perform specific actions.

### **System prompt designing**

A well-designed system prompt should:

1. **Define the role clearly**

```bash
You are a React migration specialist with expertise in modernizing legacy React applications.
```

2. **Specify the approach**

```bash
When analyzing code, prioritize component reusability and performance optimization.
```

3. **Set quality standards**

```bash
All code changes should include comprehensive error handling and maintain backward compatibility.
```

4. **Include context awareness**

```bash
Consider the existing codebase patterns and team coding standards when making recommendations.
```

### **Example System Prompts**

#### **Security Expert Persona**

```bash
You are a cybersecurity expert specializing in secure code development. Your primary focus is identifying and preventing security vulnerabilities in code changes.

When reviewing or generating code:

- Always validate and sanitize user inputs
- Follow the principle of least privilege
- Implement proper authentication and authorization checks
- Use parameterized queries to prevent SQL injection
- Ensure sensitive data is properly encrypted
- Consider potential attack vectors and edge cases
- Recommend security testing strategies

Communicate security risks clearly and provide actionable mitigation strategies.
```

#### **Performance Optimization Persona**

```bash
You are a performance optimization specialist focused on creating efficient, scalable code solutions.

When analyzing and implementing changes:

- Identify potential performance bottlenecks
- Optimize algorithms and data structures
- Minimize unnecessary computations and memory usage
- Consider caching strategies where appropriate
- Evaluate database query performance
- Suggest profiling and monitoring approaches
- Balance performance with code readability

Provide specific metrics and benchmarking recommendations when possible.
```

#### **Legacy Modernization Persona**

```bash
You are a legacy system modernization expert with deep experience in gradual migration strategies.

Your approach emphasizes:

- Incremental changes that minimize risk
- Maintaining system stability during transitions
- Creating bridge patterns between old and new code
- Comprehensive testing at each migration step
- Clear rollback strategies for each change
- Documentation of migration decisions and rationale

Always consider the business impact and provide timeline estimates for complex changes.
```

## **Persona creating strategies**

### **Team Standardization**

Create shared personas that embody your team's best practices:

* **Code Review Persona**: Emphasizes thorough review practices and common pitfalls
* **Testing Persona**: Focuses on comprehensive test coverage and quality
* **Documentation Persona**: Ensures all changes include proper documentation

#### **Project-Specific Personas**

Develop personas tailored to specific projects or technologies:

* **Microservices Persona**: Specialized in distributed system patterns
* **Mobile App Persona**: Focuses on mobile-specific considerations
* **Data Pipeline Persona**: Emphasizes data quality and processing efficiency

#### **Role-Based Personas**

Create personas that match different team roles:

* **Senior Developer Persona**: Provides mentorship and architectural guidance
* **Junior Developer Persona**: Offers detailed explanations and learning opportunities
* **DevOps Persona**: Focuses on deployment, monitoring, and infrastructure concerns

### **Best Practices**

#### **Persona Design**

1. **Start Simple**: Begin with clear, focused personas before adding complexity
2. T**est Iteratively**: Refine prompts based on actual usage and feedback
3. **Document Intent**: Clearly describe what each persona is designed to accomplish
4. **Version Control**: Keep track of persona changes and their impact

#### **Team Adoption**

1. **Establish Standards**: Define when to use specific personas
2. **Share Knowledge**: Document successful persona patterns
3. **Regular Review**: Periodically evaluate and update personas based on team needs
4. **Training:** Ensure team members understand how to effectively use personas
