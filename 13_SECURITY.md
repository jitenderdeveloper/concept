# Security Overview — MONITORING Platform

## Security Principles

| Principle               | Description                                               |
| ----------------------- | --------------------------------------------------------- |
| Zero Trust              | Every request verified — no implicit trust                |
| Least Privilege         | Each role gets minimum required access                    |
| End-to-End Encryption   | Data encrypted in transit (TLS 1.3) and at rest (AES-256) |
| Tamper-Proof Audit Logs | Append-only, immutable, time-stamped logs                 |
| Privacy First           | Parent consent required, data minimization policy         |
| Fraud Detection         | Fake GPS, proxy attendance, device abuse detection        |
| Device Binding          | One account bound to limited trusted devices              |

---

## Security Layers Diagram

```mermaid
flowchart TD
    USER[User / Client] --> TLS[TLS 1.3 Encryption]
    TLS --> WAF[Web Application Firewall]
    WAF --> DDOS[DDoS Protection]
    DDOS --> RL[Rate Limiter]
    RL --> APIGW[API Gateway]
    APIGW --> JWT[JWT Token Validation]
    JWT --> DEVICE[Device Binding Check]
    DEVICE --> RBAC[RBAC Permission Check]
    RBAC -->|Allowed| SVC[Service Handler]
    RBAC -->|Denied| ERR[403 Forbidden]
    SVC --> SANITIZE[Input Sanitization]
    SANITIZE --> DB[(Encrypted Database)]
    SVC --> AUDIT[Immutable Audit Log]
```

---

## Authentication Security

```mermaid
flowchart LR
    LOGIN[Login Request] --> CRED[Validate Credentials]
    CRED --> DEVICE_CHECK[Check Device Binding]
    DEVICE_CHECK -->|New Device| ALERT_ADMIN[Alert Admin + User]
    DEVICE_CHECK -->|Known Device| TOKEN[Issue JWT + Refresh Token]
    TOKEN --> STORE[Secure Storage on Device]

    REFRESH[Token Refresh] --> VALIDATE[Validate Refresh Token]
    VALIDATE -->|Valid| NEW_TOKEN[Issue New Access Token]
    VALIDATE -->|Expired| RELOGIN[Force Re-login]

    BRUTE[Failed Login Attempts] --> COUNTER[Increment Counter]
    COUNTER -->|Threshold Reached| LOCK[Lock Account]
    LOCK --> NOTIFY[Notify Admin]
```

---

## Data Encryption

| Data Type          | Encryption Method   | Where                |
| ------------------ | ------------------- | -------------------- |
| API Traffic        | TLS 1.3             | In Transit           |
| Passwords          | Argon2 / bcrypt     | At Rest (DB)         |
| Location Data      | AES-256             | At Rest (DB)         |
| Attendance Records | AES-256             | At Rest (DB)         |
| Auth Tokens        | Signed JWT (RS256)  | In Transit + Storage |
| Mobile Storage     | Keychain / Keystore | On Device            |

---

## Fraud Detection Rules

| Fraud Type                 | Detection Method                  | Action               |
| -------------------------- | --------------------------------- | -------------------- |
| Fake GPS                   | Mock location API detection       | Alert + Flag student |
| Proxy Attendance           | Device mismatch + location check  | Alert + Restrict     |
| Multiple Device Login      | Device fingerprint comparison     | Alert Admin          |
| Impossible Geo Movement    | Velocity anomaly (speed check)    | Flag + Alert         |
| Repeated Manual Attendance | Frequency threshold check         | Alert Principal      |
| QR Misuse                  | Time window + location validation | Reject + Log         |

---

## Risk Scoring System

```mermaid
flowchart LR
    EVENTS[Security Events] --> SCORER[Risk Score Engine]
    SCORER --> GEO_SCORE[Geo Violation Score]
    SCORER --> ATT_SCORE[Attendance Fraud Score]
    SCORER --> DEVICE_SCORE[Device Risk Score]
    GEO_SCORE --> TOTAL[Total Risk Score]
    ATT_SCORE --> TOTAL
    DEVICE_SCORE --> TOTAL
    TOTAL -->|Low| NORMAL[Status: Normal]
    TOTAL -->|Medium| WARNING[Status: Warning]
    TOTAL -->|High| HIGH_RISK[Status: High Risk]
    HIGH_RISK --> AUTO_ACTION[Auto Alert + Restrict]
```

---

## Infrastructure Security

| Layer              | Security Measure                            |
| ------------------ | ------------------------------------------- |
| Network            | Private VPC, no public DB access            |
| Firewall           | WAF + Security Groups                       |
| Secrets            | AWS Secrets Manager / GCP Secret Manager    |
| DB Access          | Private subnet only, encrypted connections  |
| Backup             | Encrypted backups, geo-redundant storage    |
| DDoS               | AWS Shield / Cloudflare protection          |
| Vulnerability Scan | Regular SAST + DAST scans in CI/CD pipeline |
