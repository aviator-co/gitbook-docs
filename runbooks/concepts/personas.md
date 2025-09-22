# Personas

Runbook Personas are customizable AI behavior profiles that define how the system interacts with your code during runbook generation, step execution, and Q\&A sessions. They allow you to tailor the AI's approach, expertise level, and communication style to match your team's specific needs and project requirements.

## **What Are Runbook Personas?**

A Runbook Persona is a configuration that defines a system prompt that influences AI behavior and decision making. You can use these personas for specialized expertise for different domains, for example, planning, development, security, etc. Personas can also be used to modify the communication style - make it more casual or strict, or be creative “answer like Yoda”. Personas can also be used to provide context awareness for specific project types or methodologies.

Personas ensure consistent, context-appropriate assistance across all runbook interactions, whether you're generating migration plans, executing code changes, or discussing implementation details.

The system prompt provided in the personas are directly passed to the background agents that are interacting with the LLMs.

## **Persona Types**

### **Built-in Persona Types**

* **Planner**: used for strategic planning and high-level architecture. Focuses on comprehensive planning, risk analysis, and step sequencing.
* **Developer**: used for code implementation and technical execution. Emphasizes practical implementation, code quality, and technical details.
* **Custom**: user-defined specialized roles. Completely customizable behavior based on your specific needs

### **Default System Personas**

The system includes default personas that provide baseline functionality:

* **Default Planner**: Comprehensive planning with emphasis on safety and thoroughness.
* **Default Developer**: Practical code implementation focused on quality and maintainability

These personas cannot be deleted but can be customized by admins.

## **Sharing and Permissions**

Personas can be private or shared. A private persona is created by an individual and can only be used by them. These personas exist for personal preferences, experimental configurations. The creator can view, edit and delete the persona themselves.

A shared persona on the other hand can only be created by admins and maintainers. These are visible to all users in the organization. Some use cases for this include team standards, organization-wide best practices. Only admins and maintainers can created, edit or delete these personas. All the users can read the system prompts of these personas.
