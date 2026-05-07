# Endpoint Naming Standards

## Base Path

Current backend API path:

```text
/api/v1
```

## Resource Naming

- Use plural nouns for collections: `/tasks`, `/schedules`, `/projects`.
- Use nested resources only when ownership is real: `/projects/{projectId}/tasks`.
- Use verbs sparingly for actions that are not CRUD resources: `/auth/login`, `/auth/logout`, `/auth/refresh`.
- Use path params for identity, query params for filtering.
- Avoid encoding state transitions in ambiguous endpoint names.

## Examples

Good:

```text
GET /api/v1/tasks
PATCH /api/v1/tasks/{taskId}
POST /api/v1/projects/{projectId}/members
PATCH /api/v1/projects/{projectId}/tasks/{taskId}/move
```

Avoid:

```text
POST /api/v1/doTaskStuff
GET /api/v1/projectTaskData
POST /api/v1/updateProject
```

## Naming Changes

Renaming an endpoint is a breaking change unless the old endpoint remains supported during a documented deprecation window.
