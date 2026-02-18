
# PROJECT BLUEPRINT — API ARCHITECTURE

## API STYLE
- REST + Realtime (WebSocket)
- JSON based
- JWT Secured
- Rate Limited
- Multi‑tenant (College आधारित)

---

## AUTH APIs
POST /auth/login  
POST /auth/logout  
POST /auth/refresh  
POST /auth/device-bind  
POST /auth/register-student  
POST /auth/register-parent  

---

## ATTENDANCE APIs
POST /attendance/qr-scan  
POST /attendance/manual-add  
POST /attendance/enter  
POST /attendance/exit  
GET /attendance/daily  
GET /attendance/monthly-report  

---

## GEO TRACKING APIs
POST /geo/update-location  
GET /geo/status  
GET /geo/timeline  
POST /geo/radius-check  

---

## NOTIFICATION APIs
POST /notify/send  
GET /notify/list  
POST /notify/read  

---

## REPORT APIs
GET /reports/attendance  
GET /reports/performance  
GET /reports/discipline  
GET /reports/export  

---

## ADMIN / MANAGEMENT APIs
POST /admin/create-user  
POST /admin/set-permission  
POST /admin/set-timing  
POST /admin/set-geo-radius  
POST /admin/add-holiday  
GET /admin/audit-logs  

---

## SECURITY
- JWT + Refresh Token
- Device Binding
- RBAC + Permission Check
- Input Validation
- Rate Limiting
