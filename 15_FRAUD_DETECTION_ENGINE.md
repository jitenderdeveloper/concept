# Fraud Detection Engine — MONITORING Platform

## Overview

The Fraud Detection Engine runs as a dedicated microservice that continuously analyzes events, calculates risk scores, and triggers alerts or restrictions when fraud patterns are detected.

---

## Fraud Detection Architecture

```mermaid
flowchart LR
    subgraph EventSources[Event Sources]
        ATT[Attendance Service]
        GEO[Geo Service]
        AUTH[Auth Service]
        APP[Mobile App]
    end

    subgraph FraudEngine[Fraud Detection Engine]
        COLLECTOR[Event Collector]
        ANALYZER[Pattern Analyzer]
        SCORER[Risk Score Calculator]
        RULE_ENGINE[Rule Engine]
    end

    subgraph Actions[Actions]
        ALERT_ADMIN[Alert Admin]
        FLAG[Flag Student]
        RESTRICT[Restrict Account]
        LOG[Log to Audit]
    end

    ATT --> COLLECTOR
    GEO --> COLLECTOR
    AUTH --> COLLECTOR
    APP --> COLLECTOR

    COLLECTOR --> ANALYZER
    ANALYZER --> SCORER
    SCORER --> RULE_ENGINE

    RULE_ENGINE -->|Low Risk| LOG
    RULE_ENGINE -->|Medium Risk| ALERT_ADMIN
    RULE_ENGINE -->|High Risk| FLAG
    FLAG --> RESTRICT
    RESTRICT --> LOG
```

---

## Fraud Detection Rules

### 1. Fake GPS Detection

```mermaid
flowchart LR
    GEO_UPDATE[Location Update Received] --> CHECK1{Mock Location API?}
    CHECK1 -->|Yes| FAKE_GPS[Fake GPS Detected]
    CHECK1 -->|No| CHECK2{Emulator Detected?}
    CHECK2 -->|Yes| FAKE_GPS
    CHECK2 -->|No| CHECK3{GPS Signal Consistent?}
    CHECK3 -->|No| FAKE_GPS
    CHECK3 -->|Yes| VALID[Valid Location]
    FAKE_GPS --> SCORE_UP[Increase Risk Score]
    FAKE_GPS --> ALERT[Alert Admin]
```

---

### 2. Velocity Anomaly (Impossible Travel)

```mermaid
flowchart LR
    LOC1[Location at T1] --> CALC[Calculate Distance / Time]
    LOC2[Location at T2] --> CALC
    CALC --> SPEED{Speed > Threshold?}
    SPEED -->|Yes| IMPOSSIBLE[Impossible Travel Detected]
    SPEED -->|No| NORMAL[Normal Movement]
    IMPOSSIBLE --> FLAG[Flag Student]
    IMPOSSIBLE --> ALERT[Alert Admin]
```

---

### 3. Proxy Attendance Detection

```mermaid
flowchart LR
    QR_SCAN[QR Scan Event] --> DEVICE_CHECK{Device Matches Student?}
    DEVICE_CHECK -->|No| PROXY[Proxy Attendance Detected]
    DEVICE_CHECK -->|Yes| LOC_CHECK{Location Near Campus?}
    LOC_CHECK -->|No| PROXY
    LOC_CHECK -->|Yes| VALID[Valid Attendance]
    PROXY --> REJECT[Reject Attendance]
    PROXY --> ALERT[Alert Admin]
    PROXY --> SCORE_UP[Increase Risk Score]
```

---

## Risk Scoring Matrix

| Event Type                    | Risk Points | Threshold for Action |
| ----------------------------- | :---------: | :------------------: |
| Fake GPS detected             |     +30     |     Alert at 30      |
| Impossible travel detected    |     +25     |     Alert at 25      |
| Proxy attendance attempt      |     +40     |     Alert at 40      |
| Multiple device login         |     +20     |     Alert at 20      |
| Repeated manual attendance    |  +10/event  |     Alert at 30      |
| QR scan outside time window   |     +5      |     Alert at 20      |
| Geo violation (out of radius) |  +5/event   |     Alert at 25      |

---

## Risk Level Classification

```mermaid
flowchart LR
    SCORE[Total Risk Score] -->|0-20| NORMAL[Normal - No Action]
    SCORE -->|21-50| WARNING[Warning - Alert Admin]
    SCORE -->|51-80| HIGH[High Risk - Flag + Restrict]
    SCORE -->|81+| CRITICAL[Critical - Auto Suspend]

    WARNING --> MONITOR[Increased Monitoring]
    HIGH --> PRINCIPAL_ALERT[Alert Principal]
    CRITICAL --> SUSPEND[Suspend Account]
    CRITICAL --> SUPERADMIN[Alert SuperAdmin]
```

---

## Fraud Engine Actions

| Risk Level | Action                                                      |
| ---------- | ----------------------------------------------------------- |
| Low        | Log event to audit trail                                    |
| Medium     | Alert Admin + Teacher + increase monitoring frequency       |
| High       | Alert Principal + Flag student + restrict manual attendance |
| Critical   | Auto suspend account + Alert SuperAdmin + full audit review |

---

## Advanced Detection (AI/ML - Future)

- Behavior pattern learning per student
- Geo spoof pattern AI detection
- Anomaly detection using historical attendance data
- Predictive risk scoring based on trends
