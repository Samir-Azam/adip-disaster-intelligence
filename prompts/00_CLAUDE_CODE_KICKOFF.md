# ADIP — Claude Code Kickoff (v3)

This replaces the "build everything in one uninterrupted pass" framing of
`01_BUILD_ENTIRE_PROJECT.md`. That framing is fine in spirit — do not ask for approval
after every file — but it has one real failure mode worth designing around: a single
giant session that runs out of context, gets interrupted, or silently drifts from the
docs has no way to resume correctly, and nobody can tell what's real vs. planned
afterward. This prompt keeps the "keep going, don't stop for permission" instruction but
adds two things that make it survive a long or interrupted build: **checkpoints with
real verification**, and **`docs/PROJECT_STATE.md` as mandatory session memory.**

---

## 0. Before writing any code

1. Read `CLAUDE.md`.
2. Read every file in `docs/`, including `docs/decisions/*.md` (10 ADRs already
   written — do not re-derive these decisions, follow them).
3. **Read `docs/PROJECT_STATE.md`.** If it says a phase is already done, verify that
   claim against the actual repo (run the tests, check the file exists) before trusting
   it — don't take the file's word over the code's.
4. If `docs/PROJECT_STATE.md` shows an in-progress task, resume that task first before
   starting anything new.
5. If this is truly the first session (empty repo besides docs), proceed to Phase 1.

## 1. Ground rules (apply to every phase below)

- **Do not stop for approval between milestones** — this instruction from `CLAUDE.md`
  still holds. "Checkpoint" below means *self-verify and record state*, not *ask the
  user and wait*.
- **Every phase ends with an actual verification step** (run the tests, hit the
  endpoint, boot the container) — not "I wrote the code, it should work." If
  verification fails, fix it before moving to the next phase; a phase is not done until
  it's verified.
- **Update `docs/PROJECT_STATE.md` at the end of every phase**, even if you keep going
  immediately into the next one. This is what makes the build resumable if the session
  is cut off — treat it as a save point, not a status report for a human.
- **Never claim something works without having run it.** If you cannot run something
  (e.g., no GPU for a real model), say so explicitly in `PROJECT_STATE.md` rather than
  describing it as done.
- Follow the fabrication rules in `CLAUDE.md` and `13_DATA_AND_RESEARCH_POLICY.md`
  throughout, not just at the end — check the box list in `PROJECT_STATE.md` §14 before
  each phase's commit, not only at final delivery.
- Resolve doc ambiguity using the simplest defensible engineering choice, and record the
  assumption in `PROJECT_STATE.md` §7 — do not stop to ask, but do not silently guess
  either.

## 2. Phases (build in this order; each is a checkpoint, not a stop)

For each phase: build it, verify it for real, commit, update `PROJECT_STATE.md`, move on
immediately to the next phase in the same session unless a genuine blocker exists.

1. **Foundation** — monorepo structure, `docker-compose.yml` skeleton (services defined,
   even before all app code exists), `.env.example` files, base Dockerfiles, CI file
   skeleton (can fail steps that aren't implemented yet — that's expected here).
   *Verify:* `docker compose config` validates; empty services boot without crash-looping.

2. **Identity** — users/organizations tables + migration, registration, login, JWT
   issue/refresh, bcrypt hashing, RBAC middleware, `GET /auth/me`.
   *Verify:* integration test hitting real Postgres — register → login → access a
   protected route → rejected without token → rejected with wrong role.

3. **Incident Core** — incidents table/migration, `POST/GET/PATCH /incidents`,
   ownership/RBAC rules per role from `01_PRODUCT_REQUIREMENTS.md`, incident status
   state machine, audit log writes on sensitive transitions.
   *Verify:* E2E-style test: citizen creates incident → field officer sees it →
   unauthorized role blocked from verify action.

4. **GIS** — PostGIS columns/indexes on incidents/resources/shelters/hospitals, map
   query endpoints, nearest-facility query.
   *Verify:* a seeded incident and a seeded shelter return correct distance ordering via
   a real `ST_DWithin`/`ST_Distance` query, not application-side math.

5. **Evidence & AI** — MinIO integration, upload validation, `incident_evidence` table,
   Redis job enqueue, AI service `AIModelAdapter` + DEMO adapter (ADR-007), worker that
   calls the AI service and persists `ai_assessments`, human review actions
   (accept/reject/override).
   *Verify:* upload a real test image through the full pipeline in Docker Compose and
   observe a persisted `ai_assessments` row with `model_name` containing `demo`.

6. **Priority engine** — weighted scoring per ADR-008, factor persistence, explanation
   payload in the incident response.
   *Verify:* unit tests covering each factor's contribution and a "missing AI data,
   partial score" degradation case.

7. **Resource Intelligence** — resources table, assignment transaction (row locking /
   `SELECT ... FOR UPDATE` or equivalent to prevent double-assignment), release/complete
   flows.
   *Verify:* a concurrency test that fires two simultaneous assignment requests for the
   same limited resource and confirms only one succeeds.

8. **Real-time** — WebSocket auth (reuse JWT), room scoping by role/org, the documented
   event set from ADR-009, REST fallback confirmed to still work with sockets disabled.
   *Verify:* a connected test client actually receives an `incident:created` event after
   a REST POST from another client.

9. **Resilience** — Redis-down and AI-service-down behavior actually tested (not just
   described), structured error responses, rate limiting, security headers, CORS
   config.
   *Verify:* stop the AI service container, confirm incident creation still succeeds and
   the assessment shows a `failed`/`queue_unavailable` state, not a hang or 500 that
   corrupts the incident.

10. **Analytics** — real database-driven queries (no hardcoded dashboard numbers) for
    incident counts/trends/resolution/resource utilization.
    *Verify:* seed a known dataset, confirm the analytics endpoint's numbers match what
    you seeded.

11. **Frontend** — screens from `09_UI_UX_REQUIREMENTS.md`, role-aware navigation,
    reported/verified/predicted/AI-generated visual distinction, loading/error/empty
    states.
    *Verify:* each primary flow (citizen report, officer verify, admin assign) clickable
    end-to-end against the real running backend, not mocked data — take this seriously,
    it's the most commonly faked part of portfolio projects.

12. **Testing/CI completeness** — fill remaining gaps from `07_TESTING_STRATEGY.md`;
    make the GitHub Actions pipeline actually green.
    *Verify:* CI run is genuinely green, screenshot/log the result into
    `PROJECT_STATE.md`, not just "should pass."

13. **Documentation** — update `docs/15_IMPLEMENTATION_HANDBOOK.md`,
    `docs/16_FEATURE_CATALOG.md`, `docs/17_API_AND_DATA_FLOW_GUIDE.md`,
    `docs/18_INTERVIEW_DEEP_DIVE.md`, `docs/19_LEARNING_ORDER.md`, and any new
    `docs/decisions/ADR-0XX-*.md` for decisions made during the build that weren't
    already covered by the 10 pre-written ADRs.
    *Verify:* spot-check three claims in the handbook against the actual file/line they
    describe — this is where documentation silently drifts from reality.

14. **Post-build audit** — run `prompts/02_POST_BUILD_AUDIT.md` as its own pass after
    phase 13, not skipped.

## 3. If you get interrupted or run low on context mid-phase

Stop at the next safe point (don't leave a half-written migration or a broken build),
write exactly where you are into `PROJECT_STATE.md` §11 ("Current task") with enough
detail that a fresh session can resume without re-reading your reasoning, and end there.
Do not start a new phase with fewer than ~20% of remaining context budget — finish
updating `PROJECT_STATE.md` instead.

## 4. Final report (once phase 14 is done)

Produce the report format already specified in `01_BUILD_ENTIRE_PROJECT.md`'s
"FINAL OUTPUT" section — this prompt doesn't change what a *complete* build reports,
only how the build gets there safely.
