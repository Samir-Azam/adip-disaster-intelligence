# ADIP Implementation Handbook

## Purpose
This document is the post-build source of truth for understanding what the code actually implements. Claude must update it after the complete build.

## Required structure

### 1. System overview
Explain the actual deployed/local components and their responsibilities.

### 2. Repository map
For every important directory/file:
- purpose
- responsibility
- important exports/classes/functions
- dependencies

### 3. Authentication
Explain registration, login, tokens, password hashing, refresh, logout, RBAC, middleware, and failure cases.

### 4. Incident lifecycle
Explain:
create → validate → persist → evidence → AI → review → priority → assignment → closure.

Include the exact API endpoints, DB tables, frontend pages/components, and services involved.

### 5. AI pipeline
Explain:
upload → storage → queue → FastAPI → model adapter → result → persistence → UI → human review.

State whether the current model is real, demo, or adapter-only.

### 6. Priority engine
Document every factor, formula/weights, normalization, explainability output, and why the baseline was selected.

### 7. GIS
Explain coordinate systems, PostGIS queries, map rendering, clustering, nearby-resource queries, and limitations.

### 8. Resource allocation
Explain inventory, assignment transactions, recommendation logic, concurrency risks, and audit trail.

### 9. Real-time updates
Explain WebSocket lifecycle, rooms/topics, authentication, reconnect behavior, and fallback polling if implemented.

### 10. Notifications
Explain provider interface, in-app notifications, delivery status, retries, and future adapters.

### 11. Security
Map each security control to actual code.

### 12. Testing
List unit/integration/E2E tests and what business behavior each protects.

### 13. Deployment
Explain Docker services, networks, volumes, environment variables, migrations, CI, and observability.

### 14. Failure modes
For each external dependency/service, explain what happens when it is unavailable.

### 15. Performance
Document measured or tested behavior. Never invent benchmarks.

### 16. Known limitations
Be explicit.

### 17. Future improvements
Prioritize improvements by user value and technical value.

### 18. 60-second interview explanation
Provide a natural spoken explanation.

### 19. Deep interview questions
At least 30 questions with concise reasoning-based answers.

### 20. Feature-to-code matrix
For every major feature:
feature → frontend → backend → database → queue/AI → tests → docs.
