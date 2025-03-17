# Aviator Agents Data Usage & Retention Policy

## **Models Used**

We primarily use **Claude Sonnet 3.7** for LLM-based tasks internally. While other models may be evaluated in the future, our primary dependency is on Claude Sonnet 3.7.

## **Data Retention Policy**

* We **do not** save the code generated during a migration.
* We **do** retain chatbot conversation history for 1 year. Not applicable for on-prem.
* No user data or generated code is used for retraining or fine-tuning models.
* Logs are maintained for compliance and monitoring but we do not store code snippets. Logs are retained for 30 days.

## **Data Usage & Security Guidelines**

1. **No Training on User Data**
   * We ensure that our LLM does **not** use user-provided inputs for training.
   * Conversations and code shared are **not incorporated** into model updates or fine-tuning.
   * More info: [Anthropic’s API Data Retention Policy](https://privacy.anthropic.com/en/articles/7996875-can-you-delete-data-that-i-sent-via-api)
2. **Copyrighted Code Assurance**
   * The latest version of the Claude API offers a copyright assurance similar to that of GitHub Copilot
     * Customers have ownership rights to generated output and legal protection against copyright claims to AI generated code.
     * More info: [Anthropic’s Copyrighted Code Assurance Policy](https://www.anthropic.com/news/expanded-legal-protections-api-improvements)
