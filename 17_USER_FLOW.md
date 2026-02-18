# User Flow — MONITORING Platform

## Complete User Journey Overview

```mermaid
flowchart TD
    subgraph StudentJourney[Student Journey]
        S1[Install App] --> S2[Register via Teacher Code]
        S2 --> S3[Login]
        S3 --> S4[Dashboard]
        S4 --> S5[Scan QR - ENTER]
        S5 --> S6[Geo Tracking ON]
        S6 --> S7[Campus Activities]
        S7 --> S8[Scan QR - EXIT]
        S8 --> S9[Geo Tracking OFF]
        S9 --> S10[Report Generated]
    end

    subgraph ParentJourney[Parent Journey]
        P1[Receive Credentials via Email] --> P2[First Login + Password Reset]
        P2 --> P3[Dashboard]
        P3 --> P4[Track Child Live Location]
        P3 --> P5[View Attendance]
        P3 --> P6[Receive Alerts]
        P3 --> P7[View Monthly Report]
    end

    subgraph TeacherJourney[Teacher Journey]
        T1[Login] --> T2[Dashboard]
        T2 --> T3[View Assigned Students]
        T2 --> T4[Monitor Attendance]
        T2 --> T5[Add Manual Attendance]
        T2 --> T6[Send Alerts]
        T2 --> T7[View Geo Violations]
    end

    subgraph PrincipalJourney[Principal Journey]
        PR1[Login] --> PR2[Dashboard]
        PR2 --> PR3[Configure Entry/Exit Time]
        PR2 --> PR4[Set Geo Radius]
        PR2 --> PR5[Manage Roles & Permissions]
        PR2 --> PR6[View Analytics]
        PR2 --> PR7[Export Reports]
        PR2 --> PR8[Add Holidays]
    end
```

---

## Student App Flow (Detailed)

```mermaid
sequenceDiagram
    participant Student
    participant App
    participant Server
    participant GeoService
    participant Parent

    Student->>App: Open App
    App->>Server: Validate JWT Token
    Server-->>App: Token Valid

    Student->>App: Scan QR Code
    App->>Server: Validate QR + Time Window
    Server-->>App: Valid - Show ENTER/EXIT

    Student->>App: Select ENTER
    App->>Server: POST /attendance/enter
    Server->>GeoService: Enable Geo Tracking
    Server-->>App: ENTER Confirmed

    loop Every N Minutes
        App->>GeoService: Send Location
        GeoService->>GeoService: Check Geofence
        alt Out of Radius
            GeoService->>Server: Geo Violation
            Server->>Parent: Alert Notification
        end
    end

    Student->>App: Scan QR Code
    App->>Server: POST /attendance/exit
    Server->>GeoService: Disable Tracking (Admin/Teacher)
    Server-->>App: EXIT Confirmed
```

---

## Parent Monitoring Flow

```mermaid
flowchart LR
    PARENT[Parent App] --> LIVE_MAP[Live Map View]
    PARENT --> ATT_VIEW[Attendance View]
    PARENT --> ALERTS_VIEW[Alerts View]
    PARENT --> REPORT_VIEW[Monthly Report View]

    LIVE_MAP -->|Real-time| CHILD_LOC[Child Location on Map]
    ATT_VIEW -->|Daily| ENTER_EXIT[ENTER/EXIT Times]
    ALERTS_VIEW -->|Push| GEO_ALERT[Geo Violation Alert]
    ALERTS_VIEW -->|Push| LATE_ALERT[Late Entry Alert]
    ALERTS_VIEW -->|Push| ABSENT_ALERT[Absent Alert]
    REPORT_VIEW -->|Monthly| SCORE[Attendance + Discipline Score]
```

---

## Teacher Workflow

```mermaid
flowchart TD
    LOGIN[Teacher Login] --> DASH[Teacher Dashboard]
    DASH --> VIEW_CLASS[View Class Attendance]
    DASH --> MANUAL_ATT[Add Manual Attendance]
    DASH --> GEO_MONITOR[Monitor Geo Violations]
    DASH --> SEND_ALERT[Send Alert to Student/Parent]

    VIEW_CLASS --> FILTER{Filter By}
    FILTER -->|Date| DATE_VIEW[Date-wise Attendance]
    FILTER -->|Student| STUDENT_VIEW[Student-wise Attendance]

    MANUAL_ATT --> ENTER_FORM[Fill ENTER + EXIT + Reason]
    ENTER_FORM --> SUBMIT[Submit Manual Attendance]
    SUBMIT --> AUDIT[Logged to Audit Trail]
```

---

## System Value Summary

| Stakeholder | Value Delivered                                              |
| ----------- | ------------------------------------------------------------ |
| Student     | Easy QR attendance, transparent records, no proxy possible   |
| Parent      | Real-time child safety, live location, instant alerts        |
| Teacher     | Automated attendance, easy manual override, class insights   |
| Principal   | Full control, analytics, fraud detection, compliance reports |
| Institution | Reduced admin work, improved discipline, SaaS scalability    |
