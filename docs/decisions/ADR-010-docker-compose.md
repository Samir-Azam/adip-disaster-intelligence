# ADR-010: Docker Compose for local orchestration (no Kubernetes)

## Status
Accepted

## Context
ADIP has six local services (frontend, backend, ai-service, postgres/postgis, redis,
minio) that need to run together with correct startup ordering, shared networking, and
a one-command local developer experience (`14_LOCAL_DEVELOPMENT.md`: clone → env setup →
docker compose → migrations → seed → open UI → tests). There is no production
deployment target defined yet in this project's scope.

## Decision
Use Docker Compose as the sole orchestration mechanism for local development and CI
smoke tests. Each service gets its own Dockerfile; `docker-compose.yml` wires networking,
volumes (Postgres data, MinIO data), and environment variables from `.env`.

## Alternatives considered
- **Kubernetes (even a local kind/minikube setup)** — massively more operational
  ceremony (manifests, ingress, secrets management) than a single-developer local MVP
  needs, and explicitly excluded by `CLAUDE.md`'s "no unnecessary
  microservices/Kubernetes" guidance. Rejected for this phase; a legitimate future step
  once/if a real production deployment is scoped.
- **Bare "run each service manually in its own terminal" (no orchestration)** —
  technically simplest, but fails the "one-command local setup" usability requirement
  and makes CI smoke testing harder to reproduce consistently. Rejected.
- **Nix/dev-container only, no containerized services** — would still require
  something to run Postgres/Redis/MinIO consistently across machines; Docker Compose
  already solves this more directly for this stack. Rejected.

## Consequences
- `docker-compose.yml` is the executable definition of "what this system consists of" —
  it must be kept in sync with reality (no service listed that doesn't actually build/
  run, no undocumented services).
- CI runs `docker compose build` and a smoke-test stage as the final verification step,
  per `08_DEPLOYMENT.md`'s pipeline (lint → typecheck → test → build → docker build →
  smoke test).
- Production deployment is explicitly **out of scope** and must not be implied by the
  existence of a working Compose file — `08_DEPLOYMENT.md` states plainly that
  "production is a future step," and the handbook must repeat this rather than let a
  working local demo be mistaken for production readiness.

## Failure mode notes
Compose's `depends_on` with health checks (not just container-start ordering) is
required for Postgres/Redis/MinIO so the backend doesn't race service startup on first
`docker compose up` — this must be verified, not assumed, during the build.
