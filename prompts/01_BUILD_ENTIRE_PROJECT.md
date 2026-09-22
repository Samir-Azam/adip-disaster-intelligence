# Claude Code — Build the Entire ADIP Project

You have been given `CLAUDE.md` and the complete `docs/` directory.

The user explicitly wants the **entire coherent MVP implemented in one continuous build**, because they have limited time. Do not stop after Milestone 0 or wait for approval between milestones.

## Phase A — Understand
1. Read CLAUDE.md.
2. Read all docs.
3. Inspect repository.
4. Resolve contradictions using the simplest defensible engineering choice.
5. Record major decisions in ADRs.

## Phase B — Build
Implement the complete MVP:
- monorepo
- frontend
- backend
- authentication/RBAC
- PostgreSQL/PostGIS
- incident reporting
- evidence upload
- object storage
- Redis
- async AI pipeline
- AI assessment adapter
- human review
- explainable priority engine
- resource management/assignment
- shelters/hospitals
- GIS map
- real-time updates
- notifications
- audit log
- analytics
- demo seed data
- tests
- Docker Compose
- CI foundation

## Phase C — Quality
Run:
- frontend lint/typecheck/tests/build
- backend lint/typecheck/tests/build
- Python lint/tests
- integration tests
- Docker build/compose validation

Fix important failures rather than merely reporting them.

## Phase D — Documentation
Update:
- docs/15_IMPLEMENTATION_HANDBOOK.md
- docs/16_FEATURE_CATALOG.md
- docs/17_API_AND_DATA_FLOW_GUIDE.md
- docs/18_INTERVIEW_DEEP_DIVE.md
- docs/19_LEARNING_ORDER.md
- ADRs

The docs must describe the implementation that actually exists.

## Phase E — Final report
Provide:
1. final architecture
2. repository tree
3. feature list
4. how to run
5. test results
6. demo credentials if seeded
7. AI implementation status (real/demo/adapter)
8. important limitations
9. security summary
10. key trade-offs
11. most important files to study first
12. suggested learning order
13. interview questions
14. known technical debt

## Important
Do not claim external integrations are live unless configured and verified.
Do not fabricate model accuracy.
Do not implement autonomous emergency decisions.
Prefer a complete working local demo over broken production integrations.
