# ADR-002: Node.js + Express + TypeScript for the primary backend

## Status
Accepted

## Context
ADIP needs a backend that: serves a REST API to the React frontend, enforces RBAC
server-side, talks to PostgreSQL/PostGIS and Redis, streams WebSocket events, and
orchestrates (but does not itself run) AI inference in a separate Python service. The
team is time-constrained and the frontend is already TypeScript/React, so a shared
language reduces context-switching and allows shared types (DTOs) between frontend and
backend.

## Decision
Use Node.js + Express + TypeScript as the main product backend. Python is reserved
exclusively for the AI inference service (see ADR-003).

## Alternatives considered
- **NestJS** — more structure/DI out of the box, but adds a steeper learning curve and
  more "magic" that's harder to explain plainly in an interview setting; Express +
  explicit layering (routes → controllers → services → repositories) is more
  transparent for the stated interview-prep goal. Rejected, but noted as a reasonable
  alternative in interview answers.
- **Python (FastAPI) for everything, including the product backend** — would unify the
  stack on one language, but loses the strong TS-across-frontend/backend story and
  Node's WebSocket/event-loop ergonomics are a better fit for the real-time requirement.
  Rejected.
- **Go** — excellent for this workload but adds a third language to an already
  multi-language stack (TS + Python) for a time-constrained solo build. Rejected for
  MVP; noted as a future scalability option.

## Consequences
- Express needs explicit middleware for what frameworks like NestJS give for free:
  centralized error handling, request validation (e.g. `zod`), structured logging,
  security headers (`helmet`), rate limiting, and CORS. These must be built once as
  shared middleware, not per-route.
- Shared TypeScript types for API request/response DTOs can be published as an internal
  package or duplicated with a comment noting the source of truth (frontend `types/`
  mirrors backend `types/`), since this is a monorepo, not published packages.
- The Node backend is the *only* service allowed to write to Postgres directly; the AI
  service returns results to Node, which persists them. This keeps a single write path
  and a single place RBAC/validation is enforced.

## Failure mode notes
Express process crashes are process-supervised by Docker Compose `restart: unless-stopped`
locally; no clustering/PM2 in MVP — documented as a known limitation, not hidden.
