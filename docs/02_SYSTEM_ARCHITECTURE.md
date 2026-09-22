# System Architecture

```text
React/TypeScript UI
      |
 HTTPS/WebSocket
      v
Node.js + Express
      |
      +--> PostgreSQL + PostGIS
      |
      +--> Redis
      |       |
      |       +--> async AI job
      |                |
      |                v
      |          FastAPI AI Service
      |                |
      |                v
      |             AI model
      |
      +--> S3/MinIO evidence
      |
      +--> External adapters
              maps/weather/notifications
```

Use a modular monorepo. Keep Node as the main product backend and Python as the AI runtime. Do not split every domain into a separate service.

## Core flow
Incident → validation → persistence → evidence storage → AI queue → inference → assessment persistence → human review → priority recommendation → resource decision → audit/notification.
