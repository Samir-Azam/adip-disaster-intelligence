# Product Requirements

## Core principle
Every feature should improve a decision or operational action.

### Citizen
Report incident, upload evidence, share location, request help, view shelters/public information, track report.

### Field officer
See assigned incidents, verify/reject, upload field evidence, change severity/status, view AI assessment, update progress.

### District administrator
Live map, incident queue, AI review, resource allocation, shelter status, analytics, audit trail, reports.

### NGO/relief coordinator
Register resources, manage availability, respond to approved requests.

## MVP acceptance
- Users can authenticate.
- RBAC blocks unauthorized actions.
- Citizen can create an incident with location/evidence.
- Evidence is stored and linked.
- AI job is queued and processed asynchronously.
- AI result stores confidence/model/version and review state.
- Authorized officer can verify/override AI result.
- Priority recommendation is explainable.
- Resources can be created and assigned.
- Map shows relevant entities.
- Real-time updates appear without manual refresh.
- Audit events exist for sensitive changes.
- System survives expected service failures without corrupting core incident data.
