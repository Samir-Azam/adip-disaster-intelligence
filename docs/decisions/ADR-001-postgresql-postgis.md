# ADR-001: PostgreSQL + PostGIS as the primary datastore

## Status
Accepted

## Context
ADIP needs to persist relational, transactional data (users, incidents, resources,
assignments) alongside genuinely geospatial data (incident locations, shelter/hospital
coverage, "nearest resource" queries, map bounding-box queries). The data model has real
foreign-key relationships and needs multi-row transactional integrity (e.g. an incident
verification that also writes an audit log row and a status-change event must succeed or
fail together).

## Decision
Use PostgreSQL as the single system-of-record database, with the PostGIS extension
enabled for all geospatial columns and queries.

## Alternatives considered
- **MongoDB + geospatial indexes** — easier ad-hoc schema, but ADIP's data is
  fundamentally relational (FKs between incidents, users, resources, assignments) and
  benefits from real transactions and constraints. Rejected.
- **PostgreSQL without PostGIS, storing lat/lng as plain floats** — simpler, but loses
  spatial indexing (GiST), `ST_DWithin`/`ST_Distance` nearest-neighbor queries, and
  correct handling of geography vs. planar math. Rejected — GIS is a first-class MVP
  requirement (operations map, nearby-resource queries), not an add-on.
- **Separate spatial database (e.g. a dedicated geo service) alongside a relational DB**
  — unnecessary operational complexity for MVP scale. Rejected per "no premature
  microservices."

## Consequences
- One connection pool, one backup/restore story, one migration tool (see also the ORM/
  migration ADR if split out).
- Requires the `postgis` extension to be enabled per-database in every environment
  (local Docker image, CI, docs must all use `postgis/postgis` image, not vanilla
  `postgres`).
- Location columns use `geography(Point, 4326)` (not `geometry`) so that distance
  queries return meters directly without manual SRID reprojection — correct for a
  country/state-scale deployment area.
- Spatial queries (map bounding box, nearest shelter) require GiST indexes; these must be
  explicitly created in migrations, not assumed.
- Local dev and CI must run the PostGIS-flavored Postgres image; a plain `postgres:16`
  image will fail migrations that call `CREATE EXTENSION postgis`.

## Failure mode notes
If Postgres is unavailable, all writes fail fast (no silent queueing) — incident
creation is not eventually-consistent by design, since incident existence is the
system's core invariant.
