# ADR-008: Explainable weighted-scoring priority engine (baseline, not ML)

## Status
Accepted

## Context
District administrators need to know *why* an incident is ranked as high priority, in
order to trust and act on the recommendation, and to be able to override it
defensibly. A black-box ML ranking model would be both harder to justify with no real
training data (see ADR-007) and harder to explain to a human reviewer under time
pressure.

## Decision
Implement priority as an explainable weighted-sum scoring function over a small,
documented set of factors — e.g. reported severity, AI-assessed severity/confidence,
population-density proxy or shelter proximity, incident type risk weight, time since
report, verification status — each with a disclosed weight. Persist the individual
factor values and the resulting score/breakdown alongside the recommendation
(`ai_assessments`/a dedicated priority record), not just the final number, so the UI can
render "why this priority" per `05_AI_ARCHITECTURE.md`.

## Alternatives considered
- **Learned ranking model (learning-to-rank)** — could in principle produce a better
  ranking, but requires labeled historical incident-outcome data ADIP does not have,
  and would reintroduce the "fabricated accuracy" risk this project explicitly avoids.
  Rejected for MVP; documented as a future direction once real operational data exists.
- **Purely manual prioritization (no system score at all)** — simplest, but fails the
  MVP acceptance criterion "priority recommendation is explainable" and removes a core
  differentiator of the product (turning fragmented signals into a prioritized,
  explainable view). Rejected.
- **Single-factor priority (e.g. severity only)** — too coarse; ignores time-decay and
  proximity to vulnerable infrastructure, which real disaster triage cares about.
  Rejected.

## Consequences
- Weights are stored as named constants (not hidden magic numbers) in one place in the
  backend, versioned, and included in the explanation payload returned to the
  frontend, so "explainable" is literally true of the API response, not just the UI.
  copy.
- Because weights are hand-set rather than learned, the handbook must state plainly
  that this is a *baseline heuristic*, not a validated predictive model — matching
  `13_DATA_AND_RESEARCH_POLICY.md`'s "never fabricate performance claims."
- A human can always override the computed priority; the override and the original
  computed value are both retained for audit/analytics.

## Failure mode notes
If an input factor is missing (e.g., no AI assessment yet), the engine must degrade
gracefully — compute from available factors with a visible "partial score, AI pending"
flag rather than blocking or defaulting silently to a misleadingly precise number.
