# MVP Boundaries

The MVP is the smallest version that can prove AIBO can convert user intent into reliable structured productivity workflows.

## MVP Must Have

| Capability | Requirement |
| --- | --- |
| Auth | Browser-safe signup, login, refresh, logout, and current-user flow. |
| Tasks | Create, list, update, soft delete, restore, filter, and track task state. |
| Schedules | Create and manage schedule entries with conflict handling. |
| Projects | Create projects, columns, tasks, assignments, ordering, and activity. |
| Engine | Classify supported intents and return structured action candidates. |
| Frontend | Complete user flows for the implemented backend capabilities. |
| Docs | Accurate setup, API, maturity, and operational expectations. |

## MVP Must Not Claim

- Autonomous planning.
- LLM-level understanding.
- Production-grade deployment.
- Enterprise compliance.
- Real-time collaboration.
- External service integrations.

## Graduation Criteria

The MVP can be called complete only when frontend workflows exercise the backend APIs, CI runs on all repositories, and the feature maturity matrix no longer marks core user workflows as partial.
