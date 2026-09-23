# ADR-005: Modular monorepo (not microservices-per-domain)

## Status
Accepted

## Context
ADIP has three genuinely separate runtimes (frontend, backend, AI service) but a small
number of contributors (effectively one, for a time-constrained portfolio build). Many
disaster-response "domains" (incidents, resources, shelters, notifications) are related
and share the same database, transactions, and RBAC layer.

## Decision
Use a single repository containing `frontend/`, `backend/`, `ai-service/`, `docs/`, with
the backend internally organized by domain module (`incidents/`, `resources/`,
`shelters/`, `auth/`, `notifications/`, `audit/`) rather than splitting each domain into
its own deployable service.

## Alternatives considered
- **One microservice per domain (incidents-service, resources-service, ...)** — offers
  independent deployability and scaling, but multiplies operational surface area
  (N databases or complex sharing, N CI pipelines, distributed transactions for flows
  that cross domains like "verify incident → assign resource → notify") for no MVP-scale
  benefit. Explicitly rejected per `CLAUDE.md`: "Do not add ... premature
  microservices."
- **Fully separate repositories per service** — clean ownership boundaries, but adds
  cross-repo versioning overhead inappropriate for a single-developer MVP timeline.
  Rejected.

## Consequences
- Domain modules inside `backend/src/modules/<domain>/` each own their routes,
  controller, service, and repository files — a clear internal boundary even though
  they share one process and one database, so a future extraction to a real
  microservice (if ever needed) has a natural seam.
- Cross-domain flows (e.g., incident verification triggering a notification and an
  audit log entry) can use a single database transaction instead of distributed-saga
  complexity — a direct benefit of the monorepo/monolith choice.
- CI runs lint/typecheck/test/build per workspace (frontend, backend, ai-service) but
  from one pipeline definition, keeping feedback fast without N separate repos to keep
  in sync.

## Consequences / trade-off to state explicitly in interviews
This is a deliberate "modular monolith" choice, not a naive one — the interview answer
should name the real alternative (microservices) and explain why it's the wrong choice
*at this scale*, not that microservices are always wrong.
