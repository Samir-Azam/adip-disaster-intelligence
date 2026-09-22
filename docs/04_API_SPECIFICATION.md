# API Specification

Base `/api/v1`

Auth:
POST /auth/register
POST /auth/login
POST /auth/refresh
POST /auth/logout
GET /auth/me

Incidents:
POST /incidents
GET /incidents
GET /incidents/:id
PATCH /incidents/:id
POST /incidents/:id/verify
POST /incidents/:id/evidence
GET /incidents/:id/timeline
POST /incidents/:id/assess

Resources:
GET /resources
POST /resources
PATCH /resources/:id
POST /resources/:id/assign

Facilities:
GET /shelters
GET /hospitals

Map:
GET /map/incidents
GET /map/resources
GET /map/facilities

Notifications:
GET /notifications
PATCH /notifications/:id/read

Analytics:
GET /admin/metrics
GET /admin/audit-logs

Rules: validate, authorize, paginate, use consistent errors, avoid secrets, request IDs, idempotency for retry-sensitive operations.
