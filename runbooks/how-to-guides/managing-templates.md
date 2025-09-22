# Managing Templates

## **Using Templates**

### **Discovery and Selection**

Templates can be browsed and filtered by:

* **Categories**: Find templates by functional area
* **Keywords**: Search by name or description
* **Usage popularity**: See which templates are most commonly used
* **Recency**: Find recently created or updated templates

## **Creating Runbooks from Templates**

When using a template, you choose a particular repository and provide additional context. This information is then used to modify the Runbook to adapt to the required needs. Once a Runbook is created, it can be modified or executed like any other Runbook.

![Using an existing template](<../../.gitbook/assets/Screenshot 2025-09-21 at 5.06.08 PM.png>)

## **Template Management**

### **Usage Tracking**

The system automatically tracks how many times a template has been used, when the template was most recently selected, and which templates lead to successful completions.

### **Template Evolution**

Templates can be updated with improved steps based on usage feedback. Every update is also internally maintained with version tracking. This way, you can track which Runbook was created using which version of the template.

### **Best Practices**

#### **Writing Effective Templates**

1. **Be Specific**: Each action should be clear and unambiguous
2. **Include Context**: Explain why steps are necessary
3. **Add Verification**: Include testing and validation steps
4. **Consider Edge Cases**: Address common complications
5. **Provide Examples**: Include code snippets where helpful

#### **Template Organization**

1. **Use Clear Categories**: Choose the most specific applicable category
2. **Write Descriptive Names:** Make templates easily discoverable
3. **Maintain Consistency**: Follow established patterns within your organization
4. **Document Assumptions**: Note prerequisites and constraints

#### **Step Design Principles**

1. **Single Responsibility**: Each step should do one thing well
2. **Logical Grouping**: Related actions should be grouped together
3. **Dependency Awareness**: Order steps to respect dependencies
4. **Error Recovery**: Consider rollback and error handling

## **Advanced Features**

The following features are still experimental and not available in the public beta release. Please reach out to us to early acccess: **howto@aviator.co**.

### **Template Variables**

Support for parameterized templates that can be customized at runtime:

```markdown
#### 1.1: Update {{FRAMEWORK_NAME}} Configuration

- 1. Modify {{CONFIG_FILE}} to include new settings
- 2. Update imports from {{OLD_PACKAGE}} to {{NEW_PACKAGE}}
```

#### **Conditional Steps**

Support for optional or conditional execution paths:

```markdown
#### 1.2: [CONDITIONAL] Update TypeScript Configuration
**Only execute if project uses TypeScript**

- 1. Update tsconfig.json for new syntax
- 2. Fix type compatibility issues
```

#### **Template Inheritance**

Support for template hierarchies where specialized templates extend base templates:

```python
**# Extended React Migration Template**

- *Extends: Base Migration Template**

**## Additional React-Specific Steps**
```

### **Troubleshooting**

#### **Common Template Issues**

1. **Parsing Errors**: Ensure proper markdown formatting and section headers
2. **Missing Steps**: Verify all required phases and tasks are included
3. **Invalid Categories**: Check that categories match predefined values
4. **Execution Failures**: Review step dependencies and prerequisites
