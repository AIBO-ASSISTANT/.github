# Environment Strategy

## Environment Types

| Environment | Purpose | Current status |
| --- | --- | --- |
| Local | Contributor development | Exists through repo scripts and local services. |
| CI | Pull request validation | Governance foundation exists; service adoption pending. |
| Staging | Production-like validation | Planned. |
| Production | User-facing runtime | Not implemented. |

## Environment Variable Rules

- Each service must maintain safe examples.
- Production secrets must live in secret storage.
- Environment changes require docs and release notes.
- Defaults must be safe for local development but not unsafe in production.

## Backend Critical Environment Areas

- MongoDB connection.
- PostgreSQL connection.
- JWT settings.
- auth cookie settings.
- CORS origins.
- engine base URL and timeout.
- rate limits.
- logging level.
