# AI Architecture

## MVP AI
Implement a real model adapter boundary and a working local inference path where a suitable model/dataset can be used. If model weights/datasets cannot be redistributed, provide a deterministic demo adapter clearly labeled `DEMO` and keep the production adapter interface ready.

AI output:
- task
- prediction/result
- confidence
- model name/version
- timestamp
- processing state
- review state

## Priority engine
Start with explainable weighted scoring. Store the factor values and final recommendation so an officer can understand why an incident received a priority.

## Future
RAG over approved disaster SOPs, flood-risk prediction, satellite/drone imagery.

## Safety
AI may recommend; humans verify consequential actions. Provide unknown/abstain behavior.
