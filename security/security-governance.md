# Security Governance

## Baseline Requirements

- Authenticated resources require authorization checks.
- User-scoped data access must be scoped by authenticated identity or project permission.
- Input validation must run before business logic.
- Secrets must be externalized.
- Logs must be redacted.
- Dependency vulnerabilities must be triaged.
- Production configuration must fail closed where possible.

## Security Review Required

Security review is required for:

- auth/session changes
- token or cookie behavior
- CORS, proxy, or origin handling
- new external service integration
- logging schema changes
- dependency additions in request path
- database migration affecting user data
- deployment or secret handling changes

## Security Debt

Security debt must be tracked as issues unless it is an undisclosed vulnerability. Sensitive details follow the private reporting path in [../SECURITY.md](../SECURITY.md).
