# System Design --- MONITORING Platform

## Architecture Style

-   Microservices-based scalable architecture
-   Event-driven + real-time tracking
-   Multi-tenant (multi-college SaaS)

## Core Components

-   API Gateway
-   Auth Service
-   Attendance Service
-   Geo Tracking Service
-   Notification Service
-   Reporting & Analytics
-   Fraud Detection Engine
-   Audit Log Service

## Data Flow

Mobile App → API Gateway → Services → DB → Realtime → Dashboard

## Realtime

WebSocket / Firebase for: - Live tracking - Notifications - Attendance
updates

## Scalability

-   Horizontal scaling
-   Stateless services
-   Queue-based processing (Redis/Kafka)
