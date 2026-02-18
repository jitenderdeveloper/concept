# Mobile APK Design — MONITORING Platform (React Native)

## Platform Support

| Platform | Distribution      | Min Version  |
| -------- | ----------------- | ------------ |
| Android  | Google Play Store | Android 8.0+ |
| iOS      | Apple App Store   | iOS 13+      |

---

## App Architecture

```mermaid
flowchart LR
    subgraph AppLayers[React Native App]
        UI[UI Layer - Screens & Components]
        STATE[State Management - Redux / Zustand]
        SERVICES[Service Layer - API Calls]
        BG[Background Services]
        STORAGE[Secure Local Storage]
    end

    subgraph Backend
        APIGW[API Gateway]
        WS[WebSocket Server]
    end

    UI --> STATE
    STATE --> SERVICES
    SERVICES --> APIGW
    BG --> APIGW
    WS --> STATE
    STORAGE --> STATE
```

---

## App Screens & Navigation Flow

```mermaid
flowchart TD
    SPLASH[Splash Screen] --> CHECK{Is Logged In?}
    CHECK -->|No| LOGIN[Login Screen]
    CHECK -->|Yes| DASH[Dashboard]

    LOGIN --> REGISTER[Register via Teacher Code]
    REGISTER --> LOGIN

    DASH --> QR[QR Scanner Screen]
    DASH --> ATT[Attendance History Screen]
    DASH --> GEO[Geo Status Screen]
    DASH --> NOTIF[Notifications Screen]
    DASH --> REPORT[Reports Screen]
    DASH --> PROFILE[Profile Screen]

    QR --> ENTER_EXIT{ENTER or EXIT?}
    ENTER_EXIT -->|ENTER| ENTER_CONFIRM[ENTER Confirmed + Geo ON]
    ENTER_EXIT -->|EXIT| REASON_CHECK{Late/Early?}
    REASON_CHECK -->|Yes| REASON_FORM[Reason Form]
    REASON_CHECK -->|No| EXIT_CONFIRM[EXIT Confirmed + Geo OFF]
    REASON_FORM --> EXIT_CONFIRM

    PROFILE --> SETTINGS[Settings]
    PROFILE --> LOGOUT[Logout]
```

---

## Core Modules

| Module              | Description                                     |
| ------------------- | ----------------------------------------------- |
| Auth Module         | Login, Register, Device Bind, Token Refresh     |
| QR Scanner Module   | Camera-based QR scan, ENTER/EXIT action         |
| Attendance Module   | View attendance history, submit reasons         |
| Geo Tracking Module | Live location send, geo status view             |
| Notification Module | Push notification receive, inbox view           |
| Report Module       | Monthly report view, performance score          |
| Profile Module      | Profile view, settings, logout                  |
| Parent Module       | Live child map, child attendance, child reports |

---

## Background Services

```mermaid
flowchart LR
    BG[Background Service Manager] --> LOC[Location Tracker]
    BG --> GEO_CHECK[Geo-fence Monitor]
    BG --> FRAUD_TRIG[Fraud Detection Trigger]
    BG --> NOTI_LISTEN[Notification Listener]
    BG --> OFFLINE_SYNC[Offline Sync Manager]

    LOC -->|Every N min| API[Send to Geo API]
    GEO_CHECK -->|Violation| ALERT[Trigger Alert]
    FRAUD_TRIG -->|Anomaly| REPORT[Report to Server]
    NOTI_LISTEN -->|New Notification| PUSH[Show Push Notification]
    OFFLINE_SYNC -->|On Reconnect| SYNC[Sync Pending Data]
```

---

## Security Implementation

| Security Layer           | Implementation                            |
| ------------------------ | ----------------------------------------- |
| Secure Storage           | Encrypted Keychain / Keystore             |
| Token Storage            | Secure storage (never plain AsyncStorage) |
| Device Binding           | Device fingerprint + server validation    |
| Root/Jailbreak Detection | react-native-device-info checks           |
| Fake GPS Detection       | Mock location detection via native module |
| Certificate Pinning      | SSL pinning for API calls                 |
| Screen Recording Block   | Prevent screenshots on sensitive screens  |

---

## State Management Flow

```mermaid
flowchart LR
    ACTION[User Action] --> DISPATCH[Dispatch Action]
    DISPATCH --> REDUCER[Reducer / Store]
    REDUCER --> STATE[App State]
    STATE --> UI[Re-render UI]
    DISPATCH --> THUNK[Async Thunk]
    THUNK --> API[API Call]
    API --> DISPATCH
```
