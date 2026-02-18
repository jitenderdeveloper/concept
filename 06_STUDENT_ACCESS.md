# Student Access — MONITORING Platform

## Student Role Overview

Students are the primary users of the mobile app. They interact with the system to mark attendance, view their own data, and receive alerts.

---

## Student App Flow

```mermaid
flowchart TD
    INSTALL[Install App] --> REG[Register via Teacher Code]
    REG --> LOGIN[Login]
    LOGIN --> DASH[Dashboard]

    DASH --> QR[Scan QR Code]
    QR --> ACTION{Select Action}
    ACTION -->|ENTER| ENTER[Mark ENTER Attendance]
    ACTION -->|EXIT| EXIT[Mark EXIT Attendance]

    ENTER --> GEO_ON[Geo Tracking ON]
    GEO_ON --> MONITOR[Location Monitored]
    MONITOR -->|Violation| ALERT[Receive Geo Alert]
    EXIT --> GEO_OFF[Geo Tracking OFF]

    DASH --> VIEW_ATT[View My Attendance]
    DASH --> VIEW_PERF[View My Performance]
    DASH --> NOTIF[View Notifications]
    DASH --> PROFILE[Profile & Settings]
```

---

## Student Permissions

| Action                      | Allowed |
| --------------------------- | ------- |
| Scan QR for ENTER / EXIT    | YES     |
| View own attendance records | YES     |
| View own geo status         | YES     |
| Submit late / early reason  | YES     |
| View own performance score  | YES     |
| Receive push notifications  | YES     |
| View own monthly report     | YES     |
| Modify attendance records   | NO      |
| View other students' data   | NO      |
| Access admin features       | NO      |

---

## Student Reason Engine

```mermaid
flowchart LR
    LATE[Late ENTER] -->|Reason Required| REASON_FORM[Submit Reason Form]
    EARLY[Early EXIT] -->|Reason Required| REASON_FORM
    NO_ENTRY[No Entry] -->|Teacher Adds Reason| TEACHER_ACTION[Teacher Submits Reason]
    REASON_FORM --> AUDIT[Saved to Audit Log]
    TEACHER_ACTION --> AUDIT
```

---

## Student Status States

```mermaid
stateDiagram-v2
    [*] --> Active
    Active --> NoEntry : Did not scan QR
    Active --> ManualAttendance : Teacher added manually
    Active --> GeoViolation : Left campus radius
    NoEntry --> Active : Teacher adds reason
    ManualAttendance --> Active : Next day
    GeoViolation --> Active : Re-entered campus
    Active --> Inactive : Account suspended
    Inactive --> Active : Reactivated by Principal
```
