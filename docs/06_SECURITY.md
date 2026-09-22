# Security & Privacy

Threats: account takeover, privilege escalation, malicious uploads, prompt injection, API abuse, forged reports, data leakage.

Controls:
- secure password hashing
- short-lived tokens and refresh strategy
- server-side RBAC
- validation
- upload type/size checks
- safe filenames/object keys
- rate limiting
- security headers
- secrets via environment
- audit logs
- signed storage URLs where applicable
- no sensitive data in logs
- dependency scanning

Do not use real personal data for development.
