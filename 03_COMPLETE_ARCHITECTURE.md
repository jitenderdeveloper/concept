# Complete System Architecture — MONITORING

This document covers the full microservices architecture, service interactions, data flow, and infrastructure layout.

---

## Full Microservices Architecture

```mermaid
flowchart LR
    subgraph MobileClients[Mobile Clients]
        StudentApp[Student App]
        ParentApp[Parent App]
        TeacherApp[Teacher App]
    end

    subgraph WebClients[Web Clients]
        AdminPanel[Admin Panel]
        PrincipalDash[Principal Dashboard]
    end

    subgraph Gateway[API Gateway Layer]
        WAF[WAF / DDoS Protection]
        APIGW[API Gateway]
        RL[Rate Limiter]
        LB[Load Balancer]
    end

    subgraph CoreServices[Core Microservices]
        AUTH[Auth Service]
        ATT[Attendance Service]
        GEO[Geo Tracking Service]
        NOTI[Notification Service]
        REP[Report & Analytics Service]
        FRAUD[Fraud Detection Engine]
        AUDIT[Audit Log Service]
        USER[User Management Service]
    end

    subgraph DataLayer[Data Layer]
        PGDB[(PostgreSQL Primary)]
        PGREPLICA[(PostgreSQL Replica)]
        REDIS[(Redis Cache)]
        KAFKA[Kafka / BullMQ]
    end

    subgraph RealtimeLayer[Realtime Layer]
        WS[WebSocket Server]
        PUSH[Push Notification Service]
    end

    subgraph InfraLayer[Infrastructure]
        DOCKER[Docker Containers]
        K8S[Kubernetes Cluster]
        CDN[CDN]
        S3[Object Storage S3]
    end

    MobileClients --> WAF
    WebClients --> WAF
    WAF --> APIGW --> RL --> LB

    LB --> AUTH
    LB --> ATT
    LB --> GEO
    LB --> NOTI
    LB --> REP
    LB --> FRAUD
    LB --> AUDIT
    LB --> USER

    AUTH --> PGDB
    ATT --> PGDB
    GEO --> PGDB
    REP --> PGDB
    FRAUD --> PGDB
    AUDIT --> PGDB
    USER --> PGDB

    PGDB --> PGREPLICA

    GEO --> REDIS
    AUTH --> REDIS
    ATT --> REDIS

    ATT --> KAFKA
    GEO --> KAFKA
    KAFKA --> NOTI
    KAFKA --> REP
    KAFKA --> FRAUD

    NOTI --> WS --> MobileClients
    NOTI --> PUSH --> MobileClients

    REP --> S3
    AUDIT --> S3

    CoreServices --> DOCKER --> K8S
```

---

## Service Interaction Map

```mermaid
flowchart TD
    ATT[Attendance Service] -->|triggers| GEO[Geo Service]
    ATT -->|logs| AUDIT[Audit Log]
    ATT -->|queues| KAFKA[Kafka]
    KAFKA -->|dispatches| NOTI[Notification Service]
    KAFKA -->|feeds| FRAUD[Fraud Engine]
    KAFKA -->|generates| REP[Report Service]
    GEO -->|violation event| KAFKA
    FRAUD -->|risk score| USER[User Service]
    FRAUD -->|alert| NOTI
    USER -->|profile data| ATT
    USER -->|role check| AUTH[Auth Service]
    AUTH -->|token| APIGW[API Gateway]
```

---

## Multi-Tenant Architecture (Per College Isolation)

```mermaid
flowchart LR
    subgraph CollegeA[College A Tenant]
        QRA[QR Code A]
        StudentsA[Students A]
        DataA[(DB Schema A)]
    end

    subgraph CollegeB[College B Tenant]
        QRB[QR Code B]
        StudentsB[Students B]
        DataB[(DB Schema B)]
    end

    subgraph SharedPlatform[Shared Platform]
        APIGW[API Gateway]
        AUTH[Auth + Tenant Resolver]
        NOTI[Notification Service]
        FRAUD[Fraud Engine]
    end

    CollegeA --> APIGW
    CollegeB --> APIGW
    APIGW --> AUTH
    AUTH -->|resolve tenant| DataA
    AUTH -->|resolve tenant| DataB
    APIGW --> NOTI
    APIGW --> FRAUD
```

---

## Infrastructure Layout

```mermaid
flowchart LR
    DEV[Developer] --> GIT[GitHub]
    GIT --> CICD[CI/CD Pipeline]
    CICD --> DOCKER[Docker Build]
    DOCKER --> K8S[Kubernetes Deploy]
    K8S --> CLOUD[AWS / GCP Cloud]
    CLOUD --> MON[Prometheus + Grafana]
    CLOUD --> LOG[ELK Stack]
    CLOUD --> ALERT[Slack / Email Alerts]
```
