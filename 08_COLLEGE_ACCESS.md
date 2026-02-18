# College-Based Access — MONITORING Platform

## College Role Overview

College-level roles include **Principal** and **CollegeManagementTeam**. The Principal is the college super-admin who configures all rules. The management team handles day-to-day operations.

---

## College Admin Flow

```mermaid
flowchart TD
    LOGIN[Principal / Management Login] --> DASH[College Dashboard]

    DASH --> CONFIG[Configure College Rules]
    DASH --> MANAGE[Manage Students & Teachers]
    DASH --> MONITOR[Monitor Attendance & Geo]
    DASH --> REPORTS[View Reports & Analytics]
    DASH --> ALERTS[Manage Alerts]

    CONFIG --> TIMING[Set Entry / Exit Time]
    CONFIG --> GEO_RADIUS[Set Geo Radius]
    CONFIG --> PERMISSIONS[Assign Teacher Permissions]
    CONFIG --> HOLIDAYS[Add Holidays]

    MANAGE --> ADD_STUDENT[Add / Edit Students]
    MANAGE --> ADD_TEACHER[Add / Edit Teachers]
    MANAGE --> ASSIGN[Assign Students to Teachers]

    MONITOR --> LIVE_ATT[Live Attendance View]
    MONITOR --> GEO_MAP[Geo Map View]
    MONITOR --> VIOLATIONS[Geo Violations List]
```

---

## Principal Permissions

| Action                     | Principal | CollegeManagement |
| -------------------------- | :-------: | :---------------: |
| Configure entry/exit time  |    YES    |        NO         |
| Set geo radius             |    YES    |        NO         |
| Add holidays               |    YES    |        NO         |
| Assign teacher permissions |    YES    |        NO         |
| Add / edit students        |    YES    |        YES        |
| Add / edit teachers        |    YES    |        NO         |
| View all attendance        |    YES    |        YES        |
| Add manual attendance      |    YES    |        YES        |
| View geo (all students)    |    YES    |      Limited      |
| View reports & analytics   |    YES    |        YES        |
| Export reports             |    YES    |        YES        |
| Send notifications         |    YES    |        YES        |
| View audit logs            |    YES    |      Limited      |

---

## Time Rule Engine (Principal Configures)

```mermaid
flowchart LR
    PRINCIPAL[Principal Sets Rules] --> ENTRY_TIME[Entry Time]
    PRINCIPAL --> EXIT_TIME[Exit Time]
    PRINCIPAL --> LATE_THRESHOLD[Late Threshold]
    PRINCIPAL --> EARLY_EXIT[Early Exit Rule]
    PRINCIPAL --> GEO_RADIUS[Geo Radius in Meters]

    ENTRY_TIME --> QR_WINDOW[QR Scan Window]
    QR_WINDOW -->|30 min before| ALLOWED[Allowed]
    QR_WINDOW -->|Up to 1hr after| ALLOWED
    QR_WINDOW -->|Earlier than 30 min| WAIT[Show Wait Message]
    QR_WINDOW -->|Later than 1hr| REASON[Reason Required]
```

---

## College Dashboard Layout

```mermaid
flowchart LR
    DASH[College Dashboard] --> SUMMARY[Summary Cards]
    DASH --> LIVE_ATT[Live Attendance Table]
    DASH --> GEO_MAP[Geo Map]
    DASH --> ALERTS_PANEL[Alerts Panel]
    DASH --> REPORTS_PANEL[Reports Panel]

    SUMMARY --> TOTAL_STUDENTS[Total Students]
    SUMMARY --> PRESENT_TODAY[Present Today]
    SUMMARY --> ABSENT_TODAY[Absent Today]
    SUMMARY --> GEO_VIOLATIONS[Geo Violations Today]
    SUMMARY --> MANUAL_ATT[Manual Attendance Count]
```
