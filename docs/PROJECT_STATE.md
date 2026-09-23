# ADIP — Project State

> **Read this file first, every session, before writing any code.**
> **Update this file last, every session, before ending.**
> This is the single source of truth for "what actually exists" — if this file and the
> code disagree, the code wins, but that disagreement itself is a bug: fix the file.

Last updated: _(fill in: date + short session description)_
Updated by: Claude Code session _(N)_

---

## 1. Current phase
_(One of: Foundation / Identity / Incident Core / GIS / AI / Resource Intelligence /
Real-time / Resilience / Analytics / Testing-CI / Documentation / Post-build audit)_

## 2. What is actually implemented and working
_(Only list things that build, run, and have been exercised — not "written" but
"verified." Be specific: endpoint paths, table names, component names.)_

| Feature | Status | Verified how | Notes |
|---|---|---|---|
| Example: `POST /auth/register` | Done | integration test + manual curl | bcrypt hashing, zod validation |

## 3. Partially implemented (in progress)
_(What's started, what's missing, why it's not done yet.)_

## 4. Not yet started
_(Pull straight from `docs/16_FEATURE_CATALOG.md` — list remaining rows.)_

## 5. Known bugs
_(Numbered, with severity. Don't delete fixed ones — mark them Fixed + date, so the
history of what broke is preserved for the interview-prep "failure modes" narrative.)_

## 6. Architectural decisions made this session
_(Anything not already covered by an ADR in `docs/decisions/`. If it's a real
decision — not an implementation detail — write a new ADR file, don't just note it
here.)_

## 7. Assumptions made
_(Anywhere a doc was ambiguous and you picked the simplest defensible option, per
CLAUDE.md's "resolve contradictions using the simplest defensible engineering choice."
State the assumption explicitly so a human can override it later.)_

## 8. Technical debt / things not to "fix" without asking
_(Deliberate shortcuts taken for MVP timeline reasons. Explain why, so a future session
doesn't "fix" something that was intentionally simplified.)_

## 9. Environment / setup state
- Migrations run as of: _(last migration filename)_
- Seed data present: yes/no — which seed script
- `.env.example` files present for: backend / ai-service / frontend (check each)
- Docker Compose last verified to build/boot: _(date, which services)_

## 10. Test status
- Backend: `N` tests, last run passing/failing
- AI service: `N` tests
- Frontend: `N` tests
- E2E: which flows are covered end-to-end vs. not yet

## 11. Current task
_(Exactly what you were doing when this session ended — specific enough that the next
session can resume mid-task, e.g. "writing `resourceAssignmentService.ts`, transaction
wrapper done, need row-level locking test for the double-assignment race condition.")_

## 12. Next task
_(The next concrete step, in priority order — not a re-statement of the whole roadmap.)_

## 13. Do NOT rebuild
_(Explicit list of things that work and should be reused/extended, not regenerated from
scratch by a future session that didn't read this file carefully.)_

## 14. Fabrication check (run before every commit)
- [ ] No AI accuracy/benchmark numbers invented — DEMO adapter labeled as DEMO
- [ ] No claims of live government/emergency-service integration
- [ ] No claim of production deployment or certification
- [ ] Demo/seed data visibly labeled as demo in the UI, not just in code comments
- [ ] Handbook/feature catalog describe what the code *actually* does, verified by
      re-reading the relevant files, not by memory of what was planned
