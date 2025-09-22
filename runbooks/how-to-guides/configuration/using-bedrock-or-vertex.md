# Using Bedrock or Vertex

Integrate with AWS Bedrock or GCP Vertex AI for enterprise LLM deployment.

## AWS Bedrock Setup

```yaml
bedrock:
  region: "us-east-1"
  credentials:
    type: "service_account"
    role_arn: "arn:aws:iam::123456789:role/agentic-bedrock"
    model_id: "anthropic.claude-4-opus"
    endpoint_config:
      max_retries: 3
      timeout: 300
```

**IAM Configuration**

```json
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Action": [
      "bedrock:InvokeModel",
      "bedrock:InvokeModelWithResponseStream"
    ],
    "Resource": "arn:aws:bedrock:*:*:model/*"
  }]
}
```

#### GCP Vertex AI Setup

```yaml
vertex:
  project_id: "my-gcp-project"
  location: "us-central1"
  credentials:
    type: "service_account"
    key_file: "/path/to/service-account.json"
    model: "gemini-1.5-pro"
    endpoint_config:
      max_retries: 3
      timeout: 300
```

**Service Account Permissions**

* `aiplatform.endpoints.predict`
* `aiplatform.models.get`

#### Benefits of Managed Services

* **Security**: VPC endpoints, IAM integration
* **Compliance**: SOC2, HIPAA, PCI options
* **Scalability**: Auto-scaling capabilities
* **Monitoring**: Native cloud observability
