# ADR Index

Status: all ten baseline ADRs below are written and committed under `docs/decisions/`.
This index is now a pointer, not a to-do list — add a new row (and a new
`docs/decisions/ADR-0XX-*.md` file) whenever a genuine architectural decision is made
during the build, per the Claude Code kickoff prompt's Phase 13.

| ADR | Decision | File |
|---|---|---|
| 001 | PostgreSQL + PostGIS as the primary datastore | [docs/decisions/ADR-001-postgresql-postgis.md](decisions/ADR-001-postgresql-postgis.md) |
| 002 | Node.js + Express + TypeScript backend | [docs/decisions/ADR-002-nodejs-express.md](decisions/ADR-002-nodejs-express.md) |
| 003 | Python + FastAPI as a separate AI inference service | [docs/decisions/ADR-003-fastapi-ai-service.md](decisions/ADR-003-fastapi-ai-service.md) |
| 004 | Redis-backed queue for asynchronous AI jobs | [docs/decisions/ADR-004-redis-async-jobs.md](decisions/ADR-004-redis-async-jobs.md) |
| 005 | Modular monorepo (not microservices-per-domain) | [docs/decisions/ADR-005-modular-monorepo.md](decisions/ADR-005-modular-monorepo.md) |
| 006 | S3-compatible object storage (MinIO for local/dev) | [docs/decisions/ADR-006-object-storage.md](decisions/ADR-006-object-storage.md) |
| 007 | Human-in-the-loop AI with an explicit adapter boundary | [docs/decisions/ADR-007-human-in-the-loop-ai.md](decisions/ADR-007-human-in-the-loop-ai.md) |
| 008 | Explainable weighted-scoring priority engine (baseline) | [docs/decisions/ADR-008-weighted-priority-baseline.md](decisions/ADR-008-weighted-priority-baseline.md) |
| 009 | WebSockets for real-time incident/resource updates | [docs/decisions/ADR-009-websockets-realtime.md](decisions/ADR-009-websockets-realtime.md) |
| 010 | Docker Compose for local orchestration (no Kubernetes) | [docs/decisions/ADR-010-docker-compose.md](decisions/ADR-010-docker-compose.md) |

## Adding a new ADR during the build

1. Copy the section headings used in ADR-001..010 (Status / Context / Decision /
   Alternatives considered / Consequences / Failure mode notes).
2. Name the file `docs/decisions/ADR-0XX-short-slug.md`, continuing the number sequence.
3. Add a row to the table above in the same commit — an ADR that isn't indexed here is
   easy to lose track of.
4. Only write one for a genuine architectural decision (a choice with real alternatives
   and consequences) — not for routine implementation details; those belong in
   `docs/PROJECT_STATE.md` §6/§7 instead.