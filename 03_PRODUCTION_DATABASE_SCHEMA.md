
# Production Database Schema (High Level)

Tables:
- users(id, role, email, password_hash, device_id)
- students(id, user_id, class, status)
- parents(id, user_id, student_id)
- attendance(id, student_id, enter_time, exit_time, type, reason)
- geo_logs(id, student_id, lat, lng, timestamp)
- notifications(id, user_id, message, status)
- audit_logs(id, actor_id, action, ts)
- roles(id, name)
- permissions(id, role_id, key)
