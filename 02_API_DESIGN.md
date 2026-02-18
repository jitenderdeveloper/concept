# API Design

## Auth APIs

-   POST /auth/login
-   POST /auth/logout
-   POST /auth/device-bind
-   POST /auth/refresh

## Attendance APIs

-   POST /attendance/qr-scan
-   POST /attendance/manual
-   POST /attendance/enter
-   POST /attendance/exit
-   GET /attendance/report

## Geo APIs

-   POST /geo/update
-   GET /geo/status
-   GET /geo/timeline

## Notification APIs

-   POST /notify/send
-   GET /notify/list

## User APIs

-   GET /user/profile
-   PATCH /user/update

## Security

-   JWT protected
-   Rate limited
-   Input validated
