# Deployment

Local Docker Compose:
frontend, backend, ai-service, postgres/postgis, redis, minio.

CI:
lint → typecheck → test → build → Docker build → smoke test.

Production is a future step. Do not claim production readiness solely because containers work.

Observability:
API latency, errors, queue depth, AI latency/failures, DB health, incident volume.
