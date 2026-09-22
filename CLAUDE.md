# ADIP — Claude Code Master Instructions

You are the lead engineer responsible for building the complete AI Disaster Intelligence Platform (ADIP) in one implementation pass.

The user has limited time. **Do not stop after each milestone and wait for approval.** Build the complete portfolio-ready MVP in one coherent pass, then document exactly what was implemented.

The user will learn the system feature-by-feature AFTER the implementation is complete.

## Core mission
Build a realistic, demonstrable disaster-response decision-support platform. It must not pretend to be an official government system and must not autonomously make consequential emergency decisions.

## Stack
- Frontend: React + TypeScript + Tailwind CSS
- Backend: Node.js + Express + TypeScript
- Database: PostgreSQL + PostGIS
- Cache/queue: Redis
- AI service: Python + FastAPI
- Object storage: S3-compatible/local MinIO for development
- Maps: MapLibre/Leaflet or another well-supported open-source mapping library
- Docker Compose for local orchestration
- GitHub Actions for CI

## Build philosophy
Prefer a complete, coherent MVP over dozens of half-built features.
Every feature must work end-to-end or be explicitly labeled as a future adapter/stub.
Do not create fake live government integrations.
Synthetic/demo data is allowed but must be visibly labeled.

## Non-negotiable safety/quality
- AI is advisory and human-reviewable.
- No autonomous emergency dispatch.
- No medical diagnosis.
- No fabricated model accuracy.
- Validate all input.
- Enforce RBAC server-side.
- Secure uploaded files.
- Never commit secrets.
- Use structured errors and logs.
- Add tests for business-critical logic.
- Use transactions where state changes must be atomic.
- Handle service failures gracefully.
- Do not add unnecessary dependencies or premature microservices.

## Important implementation rule
If a sophisticated external integration is unavailable, create a clean adapter interface and a realistic local/demo implementation. Document exactly what is real, simulated, or pending.

## Completion rule
Do NOT wait for user confirmation between milestones. Build all planned MVP functionality in one pass.

After implementation, generate/update:
- `docs/15_IMPLEMENTATION_HANDBOOK.md`
- `docs/16_FEATURE_CATALOG.md`
- `docs/17_API_AND_DATA_FLOW_GUIDE.md`
- `docs/18_INTERVIEW_DEEP_DIVE.md`
- relevant ADRs under `docs/decisions/`

The implementation handbook must describe what the code actually does, not what we hoped it would do.

## Teaching requirement
The user will learn after implementation. For every major feature document:
What → Why → How it works → Important files → Data flow → Alternatives → Trade-offs → Failure modes → Interview explanation.
