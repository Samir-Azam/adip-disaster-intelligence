# Testing Strategy

Unit: validation, priority scoring, permission checks, state transitions.
Integration: API/database, Redis queue, AI adapter, storage.
E2E: login → report → upload → AI assessment → verification → resource assignment.
Frontend: critical interaction tests.
AI: response schema, regression examples, model metadata, confidence bounds.

CI should run lint, typecheck, tests, and builds.
