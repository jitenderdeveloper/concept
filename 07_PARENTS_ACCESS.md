# Parents Access — MONITORING Platform

## Parent Role Overview

Parents have read-only access to their child's data. They can always see live location, attendance, and performance — even on holidays.

---

## Parent App Flow

```mermaid
flowchart TD
    CRED[Receive Credentials via Email] --> RESET[Reset Password on First Login]
    RESET --> LOGIN[Login to Parent App]
    LOGIN --> DASH[Parent Dashboard]

    DASH --> LIVE[View Child Live Location]
    DASH --> ATT[View Attendance Records]
    DASH --> PERF[View Performance & Discipline Score]
    DASH --> ALERTS[View Alerts & Notifications]
    DASH --> REPORT[View Monthly Report]

    LIVE --> MAP[Live Map View]
    MAP -->|Always ON| MAP
```

---

## Parent Permissions

| Action                             | Allowed |
| ---------------------------------- | ------- |
| View child live location (always)  | YES     |
| View child attendance (ENTER/EXIT) | YES     |
| View child geo activity timeline   | YES     |
| View child monthly report          | YES     |
| View child performance score       | YES     |
| Receive geo violation alerts       | YES     |
| Receive late/absent alerts         | YES     |
| View location on holidays          | YES     |
| Modify any attendance data         | NO      |
| View other students' data          | NO      |
| Access admin or teacher features   | NO      |

---

## Parent Notification Flow

```mermaid
flowchart LR
    GEO_VIOLATION[Geo Violation Detected] --> NOTI[Notification Service]
    LATE_ENTRY[Student Late Entry] --> NOTI
    NO_ENTRY[Student No Entry] --> NOTI
    EARLY_EXIT[Student Early Exit] --> NOTI
    MONTHLY[Monthly Report Ready] --> NOTI
    NOTI --> PUSH[Push Notification to Parent App]
    NOTI --> EMAIL[Email to Parent]
```

---

## Parent Data Visibility

```mermaid
flowchart LR
    PARENT[Parent App] --> CHILD_LOC[Child Live Location]
    PARENT --> CHILD_ATT[Child Attendance Log]
    PARENT --> CHILD_GEO[Child Geo Timeline]
    PARENT --> CHILD_PERF[Child Performance]
    PARENT --> CHILD_ALERTS[Child Alerts History]

    CHILD_LOC -->|Real-time| MAP[Map View]
    CHILD_ATT -->|Daily| ATT_TABLE[Attendance Table]
    CHILD_GEO -->|Timeline| GEO_LOG[Geo Activity Log]
    CHILD_PERF -->|Monthly| SCORE[Discipline Score]
```
