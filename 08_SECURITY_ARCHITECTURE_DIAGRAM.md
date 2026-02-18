
# Security Architecture

```mermaid
flowchart LR
User --> WAF
WAF --> APIGW
APIGW --> AUTH
AUTH --> DB[(Encrypted DB)]
APIGW --> LOG[Audit Logs]
APIGW --> FRAUD[Fraud Engine]
```
