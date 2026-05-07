# Production Safety Rules

Production is not a label. It is an operational state with controls.

## Required Before Production

- protected main branches
- required CI checks
- non-wildcard CORS
- HTTPS
- secure cookies
- managed secrets
- health checks
- rollback process
- backup expectations
- centralized logs
- vulnerability reporting path
- release approval process

## Dangerous Defaults

The following are not acceptable for production:

- `CORS_ORIGIN=*`
- weak `JWT_SECRET`
- insecure refresh cookies with cross-site usage
- local database connection strings
- debug logs exposing sensitive details
- manual deployment without rollback notes
- undocumented environment variables

## Release Gate

If a release touches auth, persistence, migrations, deployment, or secrets, production promotion requires explicit reviewer approval for that area.
