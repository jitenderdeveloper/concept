# Database Design — MONITORING Platform

## Database Type

| Layer      | Technology           | Purpose                          |
| ---------- | -------------------- | -------------------------------- |
| Primary DB | PostgreSQL           | All relational data              |
| Cache      | Redis                | Sessions, geo cache, rate limits |
| Queue      | Kafka / BullMQ       | Async jobs, notifications        |
| Storage    | AWS S3 / GCP Storage | Reports, exports, logs archive   |

---

## ER Diagram

```mermaid
erDiagram
    COLLEGES ||--o{ USERS : has
    COLLEGES ||--o{ HOLIDAYS : manages
    COLLEGES ||--o{ QR_CODES : owns

    USERS ||--o{ STUDENTS : creates
    USERS ||--o{ PARENTS : linked_to
    USERS ||--o{ NOTIFICATIONS : receives
    USERS ||--o{ AUDIT_LOGS : generates

    ROLES ||--o{ USERS : assigned_to
    ROLES ||--o{ PERMISSIONS : defines

    STUDENTS ||--o{ ATTENDANCE : records
    STUDENTS ||--o{ GEO_LOGS : tracks
    STUDENTS ||--|| PARENTS : linked_to

    COLLEGES {
        uuid id PK
        string name
        string address
        string qr_code
        time entry_time
        time exit_time
        int late_threshold_min
        int geo_radius_meters
        string status
        datetime created_at
    }

    USERS {
        uuid id PK
        uuid college_id FK
        uuid role_id FK
        string email
        string password_hash
        string device_id
        string status
        datetime last_login
        datetime created_at
    }

    ROLES {
        uuid id PK
        string name
    }

    PERMISSIONS {
        uuid id PK
        uuid role_id FK
        string permission_key
        boolean allowed
    }

    STUDENTS {
        uuid id PK
        uuid user_id FK
        uuid college_id FK
        uuid parent_id FK
        string class
        string section
        string roll_number
        string status
        datetime created_at
    }

    PARENTS {
        uuid id PK
        uuid user_id FK
        uuid student_id FK
        string email
        string phone
        boolean first_login_done
    }

    ATTENDANCE {
        uuid id PK
        uuid student_id FK
        date date
        datetime enter_time
        datetime exit_time
        string type
        string reason
        uuid added_by FK
        boolean late_flag
        boolean early_exit_flag
        string source_panel
    }

    GEO_LOGS {
        uuid id PK
        uuid student_id FK
        float latitude
        float longitude
        float accuracy
        boolean inside_radius
        datetime timestamp
    }

    NOTIFICATIONS {
        uuid id PK
        uuid user_id FK
        string title
        string message
        string type
        boolean is_read
        datetime created_at
    }

    AUDIT_LOGS {
        uuid id PK
        uuid actor_id FK
        string action
        string target_type
        uuid target_id
        json metadata
        datetime created_at
    }

    HOLIDAYS {
        uuid id PK
        uuid college_id FK
        date date
        string title
        uuid created_by FK
    }

    QR_CODES {
        uuid id PK
        uuid college_id FK
        string qr_data
        boolean is_active
        datetime created_at
    }
```

---

## Table Relationships Summary

```mermaid
flowchart LR
    COLLEGES --> USERS
    COLLEGES --> HOLIDAYS
    COLLEGES --> QR_CODES
    USERS --> STUDENTS
    USERS --> PARENTS
    STUDENTS --> ATTENDANCE
    STUDENTS --> GEO_LOGS
    ROLES --> USERS
    ROLES --> PERMISSIONS
    USERS --> NOTIFICATIONS
    USERS --> AUDIT_LOGS
```

---

## Indexing Strategy

| Table         | Index Columns                        | Reason                     |
| ------------- | ------------------------------------ | -------------------------- |
| users         | email, college_id, role_id           | Fast login + role lookup   |
| students      | user_id, college_id, class           | Fast student queries       |
| attendance    | student_id, date, type               | Daily attendance queries   |
| geo_logs      | student_id, timestamp, inside_radius | Real-time geo queries      |
| audit_logs    | actor_id, created_at                 | Audit trail queries        |
| notifications | user_id, is_read, created_at         | Notification inbox queries |

---

## Security Rules

- All passwords hashed with **Argon2 / bcrypt**
- Sensitive fields encrypted at rest with **AES-256**
- Audit logs are **append-only** (no UPDATE/DELETE allowed)
- DB accessible only via **private VPC network**
- Read replicas for reporting queries (no load on primary)
