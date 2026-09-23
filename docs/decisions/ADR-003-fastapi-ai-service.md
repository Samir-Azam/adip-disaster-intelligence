# ADR-003: Python + FastAPI as a separate AI inference service

## Status
Accepted

## Context
AI/ML image-assessment work benefits from Python's ecosystem (torch/onnxruntime/
PIL/numpy), which is a poor fit for the Node backend. The AI workload is also
computationally different from the CRUD/API workload (longer-running, potentially
GPU-bound in a future iteration) and should be independently scalable/restartable
without affecting the product API.

## Decision
Run a separate Python + FastAPI service (`ai-service/`) that exposes a small internal
HTTP API (e.g. `POST /assess`) consumed only by the Node backend's queue worker — never
directly by the frontend. FastAPI is chosen over Flask for native async support, request
validation via Pydantic, and auto-generated OpenAPI docs useful for interview
walkthroughs.

## Alternatives considered
- **Run inference in-process inside Node** (via a subprocess or ONNX.js) — would avoid a
  second service, but ties the product API's uptime/latency to model inference and
  forfeits Python's ML tooling. Rejected.
- **Flask** — simpler, but lacks first-class async and built-in request validation;
  FastAPI's Pydantic schemas double as the AI output contract documented in
  `05_AI_ARCHITECTURE.md`. Rejected in favor of FastAPI.
- **Managed third-party vision API (e.g. cloud vision service)** — would give real
  accuracy without training data, but introduces a live external dependency, cost, and
  contradicts "no fake live government/production integrations" framing and the
  requirement to never claim unverified accuracy. Kept as a *future adapter*, not MVP
  default — see ADR-007 (adapter interface).

## Consequences
- Two services must agree on a stable JSON contract (see `04_API_SPECIFICATION.md` /
  `05_AI_ARCHITECTURE.md`): `task`, `result`, `confidence`, `model_name`,
  `model_version`, `processing_status`.
- The AI service is *not* directly reachable from the internet/frontend — only from the
  Node backend's internal network in Docker Compose. This is a security boundary, not
  just an implementation detail (see ADR on security below / `06_SECURITY.md`).
- Requires its own dependency management (`requirements.txt`/`poetry`), its own Dockerfile,
  its own health check endpoint (`GET /health`) so Node can report AI-service-down as a
  degraded (not failed) state.

## Failure mode notes
If the AI service is unreachable or times out, the Node backend must mark the queued
job `processing_status = "failed"` with a retry policy (see ADR-004, Redis jobs) and
the incident must remain fully usable without an AI assessment — AI is advisory, never
blocking.
