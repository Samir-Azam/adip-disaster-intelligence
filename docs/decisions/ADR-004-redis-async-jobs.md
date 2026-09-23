# ADR-004: Redis-backed queue for asynchronous AI jobs

## Status
Accepted

## Context
Incident evidence upload must return immediately to the citizen; AI image assessment
can take seconds and must not block the HTTP request/response cycle. The system also
needs to survive the AI service being temporarily down without losing queued work, and
needs a straightforward way to broadcast completion to connected WebSocket clients.

## Decision
Use Redis as a job queue (via a library such as BullMQ) for AI-assessment jobs. The flow
is: evidence uploaded → Node persists incident/evidence row → Node enqueues an
`assess-incident` job to Redis → a Node worker process (or worker thread) dequeues it,
calls the FastAPI AI service, and persists the `ai_assessments` row → Node emits a
WebSocket event to subscribed clients.

## Alternatives considered
- **RabbitMQ / Kafka** — more robust delivery guarantees and better suited to
  high-throughput multi-consumer topologies, but meaningfully more operational
  complexity than this MVP's job volume justifies. Rejected per "do not add
  unnecessary Redis/queue complexity" — noted as a future scaling path if job volume
  grows.
- **Direct synchronous call from the API route to the AI service** — simplest, but
  blocks the HTTP request on AI latency and offers no retry/backoff on AI-service
  failure. Rejected — violates the "asynchronous AI processing" MVP requirement.
- **Database-polled job table (no Redis)** — avoids adding Redis as a dependency, but
  polling adds latency and load; Redis is already needed for the real-time/session
  layer in a well-designed version, so it is not truly "extra." Rejected.

## Consequences
- Redis becomes a required local dependency (`docker-compose.yml` service), with its
  own health check.
- Job payloads must be small and reference IDs (incident_id, evidence_id), never large
  binary data — the actual image lives in object storage (see ADR-006), and the job
  only carries a pointer.
- Requires an explicit retry policy (e.g. 3 attempts with exponential backoff) and a
  dead-letter/failed-state path so a permanently-failing job doesn't loop forever or
  silently vanish — surfaced as `processing_status = "failed"` visible to the reviewing
  officer.
- Redis is *not* used as a system of record. Losing the Redis queue loses in-flight
  jobs, not incident data — incidents and evidence remain fully persisted in Postgres/
  object storage regardless of queue state.

## Failure mode notes
If Redis is down at enqueue time, incident creation still succeeds; the AI assessment
step is marked `pending`/`queue_unavailable` and can be retried manually or by a
reconciliation job — it must never block or fail incident creation itself.
