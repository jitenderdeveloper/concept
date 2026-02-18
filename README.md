# MONITORING — Smart Campus Monitoring & Attendance Platform

## Overview

**MONITORING** is a Smart Campus SaaS platform for Schools, Colleges, and Institutes that provides:

- QR Based Attendance
- Real‑time Geo Tracking
- Parent Live Monitoring
- Fraud Detection
- Manual Attendance Backup
- Role & Permission Based Access
- Security & Audit Logs
- Reports & Analytics

This system improves **discipline, safety, transparency, and automation** across educational institutions.

---

# Core Features

## QR Attendance

- Unique QR per college
- ENTER / EXIT attendance
- One ENTER & EXIT per day
- Late & early exit rules
- Time window validation

## Manual Attendance

- Teacher / Principal / Management can add
- ENTER + EXIT mandatory
- Late reason required
- Audit tracking (who added)

## Geo Tracking

- Live location after ENTER
- Geo radius monitoring
- Alert if student leaves campus
- Geo timeline tracking

## Notification Engine

- Admin -> Teacher / Student
- Teacher -> Student
- Custom + system alerts
- Geo violation alerts

## Reports & Analytics

- Monthly attendance report
- Performance analytics
- Discipline score
- Geo violation tracking

## Parents Monitoring

- Live child location (always ON)
- Attendance & reports
- Alerts & notifications
- Performance tracking

## Security

- JWT + Device Binding
- RBAC + Permission
- Fake GPS detection
- Fraud detection
- Immutable audit logs
- Encrypted data

---

# Roles

- SuperAdmin
- TechTeam
- Principal
- CollegeManagement
- Teacher
- Parents
- Student

Each role has **restricted & permission-based access**.

---

# System Architecture Diagram

```mermaid
flowchart LR
Mobile[Mobile App] --> APIGW[API Gateway]
APIGW --> AUTH[Auth Service]
APIGW --> ATT[Attendance Service]
APIGW --> GEO[Geo Tracking Service]
APIGW --> NOTI[Notification Service]
APIGW --> REP[Reports Service]
APIGW --> FRAUD[Fraud Detection Engine]

AUTH --> DB[(Database)]
ATT --> DB
GEO --> DB
REP --> DB

NOTI --> WS[Realtime Engine]
WS --> Mobile
```

---

# App Flow Diagram

```mermaid
flowchart TD
A[Install App] --> B[Login / Register]
B --> C[Dashboard]
C --> D[Scan QR]
D --> E[ENTER Attendance]
E --> F[Geo Tracking ON]
F -->|User Action| H[EXIT Attendance]
F -->|Violation| G[Alerts]
H --> I[Geo Tracking OFF]
C --> J[Reports]
C --> K[Profile]
```

---

# Database ER Diagram

```mermaid
erDiagram
USERS ||--o{ STUDENTS : has
USERS ||--o{ PARENTS : linked
STUDENTS ||--o{ ATTENDANCE : records
STUDENTS ||--o{ GEO_LOGS : tracks
USERS ||--o{ NOTIFICATIONS : receives
USERS ||--o{ AUDIT_LOGS : generates
ROLES ||--o{ USERS : assigned

USERS {
string id
string email
string role
string device_id
}

STUDENTS {
string id
string user_id
string class
string status
}

ATTENDANCE {
string id
string student_id
datetime enter_time
datetime exit_time
string type
string reason
}

GEO_LOGS {
string id
string student_id
float latitude
float longitude
datetime timestamp
}
```

---

# Security Architecture Diagram

```mermaid
flowchart LR
User --> WAF
WAF --> APIGW
APIGW --> AUTH
AUTH --> DB[(Encrypted Database)]
APIGW --> LOG[Audit Logs]
APIGW --> FRAUD[Fraud Engine]
```

---

# Business Model

- SaaS subscription per student
- Per college license
- Enterprise version
- Add‑on analytics & security

---

# Future Enhancements

- Face Recognition Attendance
- AI Risk Detection
- SOS Emergency Button
- Offline Sync
- Multi‑Campus Support
- ERP Integration
- Security Dashboard

---

# Conclusion

**MONITORING** is a complete **Smart, Secure, and Scalable Campus Monitoring Platform** providing real-time tracking, fraud-resistant attendance, parent transparency, and enterprise-grade security.
