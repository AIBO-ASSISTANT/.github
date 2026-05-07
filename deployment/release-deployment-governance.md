# Release and Deployment Governance

## Release Gate

A release candidate must show:

- passing CI
- changelog entry
- migration plan when needed
- rollback plan
- environment variable changes
- security review when relevant
- feature maturity review

## Deployment Gate

A deployment must show:

- target environment
- artifact or commit SHA
- config source
- secret source
- health check behavior
- rollback command or process
- owner on call for the change window

## Rollback Rules

- Rollback must be documented before production deploy.
- Database migrations need forward and backward safety review.
- Auth/session changes require special rollback caution because clients may hold tokens or cookies from either version.
