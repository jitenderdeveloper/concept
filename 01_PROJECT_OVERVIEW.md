# MONITORING — Project Overview

**MONITORING** is a production-grade Smart Campus SaaS Platform for Schools, Colleges, Hostels, and Coaching Institutes.

It automates attendance, tracks student geo-location in real-time, provides parent visibility, detects fraud, and enforces role-based access — all in one secure, scalable platform.

---

## Goal

Provide secure QR attendance, real-time geo tracking, parent visibility, fraud detection, and complete monitoring across educational institutions.

---

## Core Pillars

| Pillar                | Description                                               |
| --------------------- | --------------------------------------------------------- |
| Attendance Discipline | QR + Manual attendance with time rules and audit trail    |
| Student Safety        | Real-time geo tracking, geo-fence alerts, SOS button      |
| Transparency          | Parents see live location, attendance, and performance    |
| Security              | JWT, device binding, RBAC, encrypted data, immutable logs |
| Fraud Prevention      | Fake GPS detection, proxy attendance, risk scoring        |
| Role-Based Control    | Fine-grained RBAC for every role in the system            |

---

## Platform Overview Diagram

```mermaid
flowchart TD
    subgraph Users
        S[Student]
        P[Parent]
        T[Teacher]
        PR[Principal]
        SA[SuperAdmin]
    end

    subgraph MobileApp[Mobile App]
        QR[QR Scanner]
        GEO[Geo Tracker]
        NOTI[Notifications]
        REP[Reports]
    end

    subgraph Backend[Backend Platform]
        APIGW[API Gateway]
        AUTH[Auth Service]
        ATT[Attendance Service]
        GS[Geo Service]
        NS[Notification Service]
        RS[Report Service]
        FD[Fraud Detection]
        AL[Audit Logs]
    end

    subgraph Data[Data Layer]
        DB[(PostgreSQL)]
        CACHE[(Redis)]
        QUEUE[Kafka Queue]
    end

    S --> MobileApp
    P --> MobileApp
    T --> MobileApp
    PR --> MobileApp
    SA --> MobileApp

    MobileApp --> APIGW
    APIGW --> AUTH
    APIGW --> ATT
    APIGW --> GS
    APIGW --> NS
    APIGW --> RS
    APIGW --> FD
    APIGW --> AL

    AUTH --> DB
    ATT --> DB
    GS --> DB
    RS --> DB
    FD --> DB
    AL --> DB

    GS --> CACHE
    ATT --> QUEUE
    QUEUE --> NS
```

---

## Key Metrics (Target)

| Metric           | Value                         |
| ---------------- | ----------------------------- |
| Supported Roles  | 8 roles                       |
| Core Modules     | 15 modules                    |
| Attendance Types | QR + Manual                   |
| Geo Tracking     | Real-time, every N minutes    |
| Alert Channels   | Push + Email + In-App         |
| Data Encryption  | AES-256 at rest, TLS 1.3      |
| Deployment       | Docker + Kubernetes + AWS/GCP |
| Target Clients   | Schools, Colleges, Hostels    |

---

## System Type

**Smart Campus Monitoring SaaS Platform**

Sell to: Schools, Colleges, Hostels, Coaching Institutes, Corporates
