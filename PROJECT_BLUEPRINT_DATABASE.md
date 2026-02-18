
# PROJECT BLUEPRINT — DATABASE DESIGN

## DATABASE TYPE
- PostgreSQL / MySQL (Relational)
- Redis (Cache)
- Timeseries (Geo logs optional)

---

## CORE TABLES

### users
id, role, email, password_hash, device_id, status, created_at

### students
id, user_id, college_id, class, status, parent_id

### parents
id, user_id, student_id, email, phone

### attendance
id, student_id, date, enter_time, exit_time, type(QR/MANUAL), reason, late_flag, manual_by

### geo_logs
id, student_id, lat, lng, inside_radius, timestamp

### notifications
id, user_id, title, message, status, created_at

### audit_logs
id, actor_id, action, target_id, metadata, created_at

### roles
id, name

### permissions
id, role_id, key, value

### holidays
id, college_id, date, title

---

## INDEXING
- student_id
- date
- geo_timestamp
- role
- college_id

---

## SECURITY
- Encrypt sensitive data
- Password → bcrypt/argon2
- Immutable audit logs
