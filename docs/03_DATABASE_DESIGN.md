# Database Design

PostgreSQL + PostGIS.

Core tables:
- users
- organizations
- incidents
- incident_evidence
- ai_assessments
- resources
- resource_assignments
- shelters
- hospitals
- notifications
- audit_logs
- incident_events

Important fields:
incidents: id, reporter_id, type, title, description, severity, status, verification_status, location, timestamps.
ai_assessments: incident_id, evidence_id, model_name, model_version, task, result JSONB, confidence, processing_status, reviewed_by, reviewed_at.
resources: type, quantity, available_quantity, location, status, organization_id.
audit_logs: actor, action, entity, metadata, timestamp.

Use foreign keys, constraints, transactions, PostGIS indexes, and measured indexes.
