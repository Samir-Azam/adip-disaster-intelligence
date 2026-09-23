# ADR-007: Human-in-the-loop AI with an explicit adapter boundary

## Status
Accepted

## Context
ADIP's AI component assesses disaster evidence (e.g., flood-severity-from-image). No
legitimately trained, redistributable production model with verified accuracy is
available for this project. The project charter explicitly rules out autonomous
emergency decisions, fabricated accuracy claims, and disguising demo behavior as real.
At the same time, the architecture must be genuine enough to demonstrate a real
production integration point.

## Decision
1. Define a stable `AIModelAdapter` interface in the AI service (`predict(image) ->
   {task, result, confidence, model_name, model_version}`).
2. Ship a **deterministic DEMO adapter** as the default implementation — clearly labeled
   `DEMO` in the model_name/version fields and in the UI — that produces plausible,
   reproducible outputs from simple, disclosed image heuristics (not a trained model
   pretending to be one).
3. Keep the adapter interface ready to swap in a real trained model (e.g., a small
   open, redistributable image classifier) without changing any calling code in Node or
   the FastAPI route layer.
4. Every AI result is written with `processing_status` and `review_state`
   (`pending_review`/`accepted`/`rejected`/`overridden`) and requires a human
   (field officer / district admin) action before it influences anything
   consequential (priority score, resource assignment) beyond an advisory display.

## Alternatives considered
- **Train a "real" model quickly on a small scraped dataset and present it as
  production-grade** — explicitly rejected: violates the "never fabricate model
  accuracy" and "no fake production results" requirements, and would be dishonest in an
  interview setting when asked about accuracy.
- **Skip AI entirely for MVP** — rejected: AI-assisted assessment and the async
  pipeline (Redis → FastAPI → adapter → review) are core, named MVP requirements and a
  key interview talking point (queueing, service boundaries, human-in-the-loop design).
- **Call a third-party vision API and present its output as ADIP's own model** —
  rejected: adds a live external dependency and misattributes provenance; if used at
  all in the future it must be clearly labeled as a third-party integration, not "our
  model."

## Consequences
- The UI must visually distinguish AI-suggested severity/confidence from
  human-verified severity at all times (per `09_UI_UX_REQUIREMENTS.md`:
  reported vs. verified vs. predicted vs. AI-generated).
- `ai_assessments.model_name` will literally read something like
  `demo-heuristic-classifier` and `model_version` will be a real semantic version the
  team controls — never a name implying a well-known production model unless that
  model is actually wired in.
- The priority engine (ADR-008) treats AI confidence as *one input factor*, never as an
  auto-approval signal — a low- or high-confidence AI result never by itself changes
  incident status without a human action.

## Failure mode notes
An AI-service outage or adapter error results in `processing_status = "failed"` and an
incident that is still fully manageable by human reviewers — the system must never
appear to "hang" waiting on AI.
