# Scheduler Lifecycle

The scheduler is currently a backend-owned workflow. The frontend schedule experience is not complete.

```mermaid
sequenceDiagram
  participant Frontend
  participant Backend
  participant Mongo as MongoDB schedules/tasks
  Frontend->>Backend: POST /api/v1/schedules
  Backend->>Backend: authenticate user
  Backend->>Backend: validate date, time, range, task relationship
  Backend->>Mongo: check conflicts and write schedule
  Mongo-->>Backend: schedule document
  Backend-->>Frontend: success envelope
```

## Backend Responsibilities

- Validate schedule payloads.
- Prevent invalid time ranges.
- Handle conflict behavior consistently.
- Keep schedule writes scoped to the authenticated user.
- Preserve local development behavior when MongoDB transaction support is unavailable.

## Governance Requirements

- Any change to conflict semantics must update API docs and tests.
- Bulk schedule behavior must define partial failure semantics.
- Calendar integrations are planned only; do not document them as active.
