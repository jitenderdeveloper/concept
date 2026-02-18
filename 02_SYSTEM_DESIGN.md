# System Design — MONITORING Platform

## Architecture Style

| Property    | Value                                |
| ----------- | ------------------------------------ |
| Pattern     | Microservices + Event-Driven         |
| Tenancy     | Multi-tenant (per college isolation) |
| Realtime    | WebSocket + Firebase                 |
| Scalability | Horizontal, stateless services       |
| Queue       | Kafka / BullMQ for async jobs        |
| Cache       | Redis for session + geo data         |

---

## Core Services

| Service                | Responsibility                                     |
| ---------------------- | -------------------------------------------------- |
| API Gateway            | Route, auth-check, rate-limit all requests         |
| Auth Service           | JWT, device binding, login anomaly detection       |
| Attendance Service     | QR scan, manual entry, time rule enforcement       |
| Geo Tracking Service   | Location updates, geofence check, timeline         |
| Notification Service   | Push, email, in-app alerts                         |
| Reporting Service      | Monthly reports, analytics, exports                |
| Fraud Detection Engine | Risk scoring, fake GPS, proxy attendance detection |
| Audit Log Service      | Immutable, time-stamped event logging              |

---

## System Design Diagram

```mermaid
flowchart LR
    subgraph Client
        APP[Mobile App]
        DASH[Admin Dashboard]
    end

    subgraph Gateway
        APIGW[API Gateway]
        RL[Rate Limiter]
        WAF[WAF]
    end

    subgraph Services
        AUTH[Auth Service]
        ATT[Attendance Service]
        GEO[Geo Tracking Service]
        NOTI[Notification Service]
        REP[Report Service]
        FRAUD[Fraud Detection Engine]
        AUDIT[Audit Log Service]
    end

    subgraph DataLayer
        DB[(PostgreSQL)]
        REDIS[(Redis Cache)]
        KAFKA[Kafka Queue]
    end

    subgraph Realtime
        WS[WebSocket Server]
        FIREBASE[Firebase Realtime]
    end

    APP --> WAF --> APIGW --> RL
    DASH --> WAF
    RL --> AUTH
    RL --> ATT
    RL --> GEO
    RL --> NOTI
    RL --> REP
    RL --> FRAUD
    RL --> AUDIT

    AUTH --> DB
    ATT --> DB
    GEO --> DB
    REP --> DB
    FRAUD --> DB
    AUDIT --> DB

    GEO --> REDIS
    AUTH --> REDIS

    ATT --> KAFKA
    KAFKA --> NOTI
    KAFKA --> REP
    KAFKA --> FRAUD

    NOTI --> WS --> APP
    NOTI --> FIREBASE --> APP
```

---

## Data Flow

```mermaid
sequenceDiagram
    participant App
    participant APIGW as API Gateway
    participant AUTH as Auth Service
    participant ATT as Attendance Service
    participant GEO as Geo Service
    participant DB as Database

    App->>APIGW: Request (with JWT)
    APIGW->>AUTH: Validate Token + Role
    AUTH-->>APIGW: Token Valid
    APIGW->>ATT: Process Attendance
    ATT->>DB: Write Attendance Record
    ATT->>GEO: Enable Geo Tracking
    GEO->>DB: Start Geo Log
    DB-->>ATT: Confirmed
    ATT-->>App: Success Response
```

---

## Scalability Design

```mermaid
flowchart LR
    LB[Load Balancer] --> S1[Service Instance 1]
    LB --> S2[Service Instance 2]
    LB --> S3[Service Instance N]
    S1 --> DB[(Primary DB)]
    S2 --> DB
    S3 --> DB
    DB --> R1[(Read Replica 1)]
    DB --> R2[(Read Replica 2)]
    S1 --> CACHE[(Redis Cluster)]
    S2 --> CACHE
    S3 --> CACHE
```

---

## Tech Stack

| Layer      | Technology                           |
| ---------- | ------------------------------------ |
| Mobile     | Flutter / React Native               |
| Backend    | Node.js / NestJS                     |
| Realtime   | WebSocket / Firebase                 |
| Database   | PostgreSQL                           |
| Cache      | Redis                                |
| Queue      | Kafka / BullMQ                       |
| Geo        | Google Maps API / Mapbox             |
| Cloud      | AWS / GCP                            |
| CI/CD      | Docker + Kubernetes + GitHub Actions |
| Monitoring | Prometheus + Grafana + ELK Stack     |
