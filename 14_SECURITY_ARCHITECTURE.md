# Security Architecture — MONITORING Platform

## Security Architecture Overview

```mermaid
flowchart LR
    subgraph Internet
        USER[User / Mobile App]
    end

    subgraph EdgeSecurity[Edge Security Layer]
        CF[Cloudflare / AWS Shield - DDoS]
        WAF[Web Application Firewall]
        CDN[CDN]
    end

    subgraph GatewaySecurity[Gateway Security Layer]
        APIGW[API Gateway]
        RL[Rate Limiter]
        JWT_MW[JWT Middleware]
        RBAC_MW[RBAC Middleware]
        SANITIZE[Input Sanitizer]
    end

    subgraph AppSecurity[Application Security Layer]
        AUTH[Auth Service]
        FRAUD[Fraud Detection Engine]
        AUDIT[Audit Log Service]
    end

    subgraph DataSecurity[Data Security Layer]
        DB[(Encrypted PostgreSQL)]
        REDIS[(Encrypted Redis)]
        S3[(Encrypted S3)]
        VPC[Private VPC Network]
        SECRETS[Secrets Manager]
    end

    subgraph MonitoringSecurity[Security Monitoring]
        SIEM[SIEM / ELK Stack]
        ALERT[Alert Manager]
        PROM[Prometheus]
    end

    USER --> CF --> WAF --> CDN
    CDN --> APIGW --> RL --> JWT_MW --> RBAC_MW --> SANITIZE
    SANITIZE --> AUTH
    SANITIZE --> FRAUD
    SANITIZE --> AUDIT

    AUTH --> VPC --> DB
    FRAUD --> VPC --> DB
    AUDIT --> VPC --> DB
    AUTH --> SECRETS
    AUTH --> REDIS

    AppSecurity --> SIEM --> ALERT
    AppSecurity --> PROM
```

---

## Request Security Flow (Detailed)

```mermaid
sequenceDiagram
    participant Client
    participant WAF
    participant APIGW as API Gateway
    participant JWT as JWT Service
    participant RBAC as RBAC Engine
    participant SVC as Microservice
    participant AUDIT as Audit Log

    Client->>WAF: HTTPS Request
    WAF->>WAF: DDoS + SQL Injection Check
    WAF->>APIGW: Forward Clean Request
    APIGW->>APIGW: Rate Limit Check
    APIGW->>JWT: Validate Bearer Token
    JWT-->>APIGW: Token Valid + User Info
    APIGW->>RBAC: Check Role Permission
    RBAC-->>APIGW: Permission Granted
    APIGW->>SVC: Forward Request
    SVC->>AUDIT: Log Action
    SVC-->>Client: Response
```

---

## Device Security Flow

```mermaid
flowchart TD
    INSTALL[App Install] --> REGISTER[Register with Teacher Code]
    REGISTER --> BIND[Bind Device Fingerprint]
    BIND --> DB[(Store Device ID)]

    LOGIN[Login Attempt] --> CHECK_DEVICE{Device Known?}
    CHECK_DEVICE -->|Known| JWT[Issue JWT Token]
    CHECK_DEVICE -->|New Device| ALERT[Alert Admin + User]
    ALERT --> VERIFY[Manual Verification Required]
    VERIFY -->|Approved| JWT
    VERIFY -->|Rejected| BLOCK[Block Login]

    REINSTALL[App Reinstall] --> TEACHER_CODE[Require Teacher Code]
    TEACHER_CODE -->|Valid| REBIND[Re-bind Device]
    TEACHER_CODE -->|Invalid| DENY[Deny Access]
```

---

## Audit Log Architecture

```mermaid
flowchart LR
    EVENTS[All System Events] --> AUDIT_SVC[Audit Log Service]
    AUDIT_SVC --> APPEND[Append-Only Write]
    APPEND --> DB[(Audit Log DB - Immutable)]
    DB --> ARCHIVE[Archive to S3 - Encrypted]

    QUERY[Admin Query] --> READ[Read-Only Access]
    READ --> DB
    READ --> ARCHIVE

    TAMPER[Tamper Attempt] --> DETECT[Detect via Hash Chain]
    DETECT --> ALERT[Alert SuperAdmin]
```

---

## Security Compliance Checklist

| Requirement                 | Status   | Implementation                         |
| --------------------------- | -------- | -------------------------------------- |
| Data Encryption at Rest     | Required | AES-256 on all sensitive fields        |
| Data Encryption in Transit  | Required | TLS 1.3 enforced                       |
| Authentication              | Required | JWT + Refresh Token + Device Binding   |
| Authorization               | Required | RBAC + Fine-grained permissions        |
| Audit Logging               | Required | Immutable, append-only audit logs      |
| Parent Consent for Tracking | Required | Consent flow on first login            |
| Data Minimization           | Required | Only collect what is needed            |
| Right to Data Deletion      | Required | Admin can delete student data          |
| Brute Force Protection      | Required | Account lock after N failed attempts   |
| Fake GPS Detection          | Required | Mock location detection in app         |
| Root/Jailbreak Detection    | Required | Block app on rooted/jailbroken devices |
