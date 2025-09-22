# Model usage

Configure and optimize LLM usage for your environment.

## Supported Models

**Claude Models**

* Claude Opus 4
* Claude Sonnet 4
* Claude Sonnet 3.7
* Claude Haiku 3.5

**Gemini Models**

* Gemini Pro 2.5
* Gemini Flash 2.5

#### Model Selection Strategy

```
Task Complexity     Recommended Model     Rationale
───────────────     ─────────────────     ─────────
Simple refactor     Haiku/Flash          Cost effective
Complex analysis    Opus/Pro             Higher accuracy
Creative solutions  Opus/Pro             Better reasoning
Bulk operations     Haiku/Flash          Token efficiency
```

#### API Configuration

```yaml
models:
  primary:
    provider: "anthropic"
    model: "claude-4-opus"
    api_key: "${ANTHROPIC_API_KEY}"
    max_tokens: 100000
    temperature: 0.2

```

## Token Optimization

1. **Context Window Management**
   * Prioritize relevant code
   * Compress historical context
   * Use incremental updates
2. **Prompt Engineering**
   * Concise, clear instructions
   * Structured output formats
   * Few-shot examples
3. **Caching Strategy**
   * Cache analysis results
   * Reuse common patterns
   * Share context across steps
