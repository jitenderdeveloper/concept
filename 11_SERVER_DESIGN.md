# Server Design — MONITORING Platform

## Backend Stack

| Component     | Technology           | Purpose                         |
| ------------- | -------------------- | ------------------------------- |
| Runtime       | Node.js / NestJS     | Core API services               |
| API Style     | REST + WebSocket     | Sync + Realtime communication   |
| Cache         | Redis                | Session, geo cache, rate limits |
| Queue         | Kafka / BullMQ       | Async background jobs           |
| Realtime      | WebSocket / Firebase | Live tracking, notifications    |
| Load Balancer | Nginx / AWS ALB      | Traffic distribution            |
| Container     | Docker               | Service packaging               |
| Orchestration | Kubernetes           | Auto-scaling, health management |

---

## Server Architecture Diagram

```mermaid
flowchart LR
    subgraph Internet
        CLIENT[Mobile / Web Client]
    end

    subgraph EdgeLayer[Edge Layer]
        CDN[CDN]
        WAF[WAF / DDoS Protection]
        LB[Load Balancer - Nginx/ALB]
    end

    subgraph AppLayer[Application Layer - Kubernetes]
        APIGW[API Gateway Service]
        AUTH[Auth Service]
        ATT[Attendance Service]
        GEO[Geo Tracking Service]
        NOTI[Notification Service]
        REP[Report Service]
        FRAUD[Fraud Detection Service]
        AUDIT[Audit Log Service]
        USER[User Management Service]
    end

    subgraph DataLayer[Data Layer]
        PGPRIMARY[(PostgreSQL Primary)]
        PGREPLICA[(PostgreSQL Read Replica)]
        REDIS[(Redis Cluster)]
        KAFKA[Kafka Broker]
        S3[Object Storage S3]
    end

    subgraph MonitoringLayer[Monitoring Layer]
        PROM[Prometheus]
        GRAFANA[Grafana]
        ELK[ELK Stack]
        ALERT[Alert Manager]
    end

    CLIENT --> CDN --> WAF --> LB
    LB --> APIGW
    APIGW --> AUTH
    APIGW --> ATT
    APIGW --> GEO
    APIGW --> NOTI
    APIGW --> REP
    APIGW --> FRAUD
    APIGW --> AUDIT
    APIGW --> USER

    AUTH --> PGPRIMARY
    ATT --> PGPRIMARY
    GEO --> PGPRIMARY
    FRAUD --> PGPRIMARY
    AUDIT --> PGPRIMARY
    USER --> PGPRIMARY

    PGPRIMARY --> PGREPLICA
    REP --> PGREPLICA

    GEO --> REDIS
    AUTH --> REDIS
    ATT --> REDIS

    ATT --> KAFKA
    GEO --> KAFKA
    KAFKA --> NOTI
    KAFKA --> REP
    KAFKA --> FRAUD

    REP --> S3
    AUDIT --> S3

    AppLayer --> PROM --> GRAFANA
    AppLayer --> ELK
    PROM --> ALERT
```

---

## Background Jobs (Queue-Based)

```mermaid
flowchart LR
    KAFKA[Kafka / BullMQ] --> JOB1[Monthly Report Generator]
    KAFKA --> JOB2[Email Sender]
    KAFKA --> JOB3[Fraud Score Calculator]
    KAFKA --> JOB4[Geo Violation Detector]
    KAFKA --> JOB5[Notification Dispatcher]
    KAFKA --> JOB6[Audit Log Archiver]

    JOB1 --> S3[S3 Storage]
    JOB2 --> EMAIL[Email Service - SES/SendGrid]
    JOB3 --> DB[(PostgreSQL)]
    JOB4 --> NOTI[Notification Service]
    JOB5 --> PUSH[Push Notification - FCM/APNs]
    JOB6 --> S3
```

---

## Service Health & Monitoring

| Tool          | Purpose                                    |
| ------------- | ------------------------------------------ |
| Prometheus    | Metrics collection (CPU, memory, requests) |
| Grafana       | Dashboard visualization                    |
| ELK Stack     | Centralized log aggregation & search       |
| Alert Manager | Slack/Email alerts on threshold breach     |
| K8s Probes    | Liveness + Readiness health checks         |

---

## Deployment Pipeline

```mermaid
flowchart LR
    DEV[Developer Push] --> GIT[GitHub]
    GIT --> CI[CI Pipeline - GitHub Actions]
    CI --> TEST[Run Tests]
    TEST -->|Pass| BUILD[Docker Build]
    TEST -->|Fail| NOTIFY_FAIL[Notify Developer]
    BUILD --> REGISTRY[Docker Registry]
    REGISTRY --> CD[CD Pipeline]
    CD --> K8S[Kubernetes Rolling Deploy]
    K8S --> HEALTH[Health Check]
    HEALTH -->|Pass| LIVE[Live Production]
    HEALTH -->|Fail| ROLLBACK[Auto Rollback]
```
