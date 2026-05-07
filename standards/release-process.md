# Release Process

Formal release automation is not implemented yet. This process defines the release discipline that service repositories should adopt.

## Versioning

Use semantic versioning once releases are formalized:

- `MAJOR` for breaking API/runtime behavior.
- `MINOR` for backward-compatible features.
- `PATCH` for backward-compatible fixes.

Before formal releases, use dated release notes and clear deployment identifiers.

## Release Requirements

- All required CI checks pass.
- Changelog entry exists.
- Feature maturity matrix is reviewed.
- Deployment target and rollback plan are known.
- Database migrations are documented.
- Security impact is reviewed.
- API changes are documented.

## Release Notes Must Include

- summary
- affected repositories
- user-visible changes
- API or migration changes
- operational changes
- known limitations
- rollback notes

## Prohibited Release Claims

Do not claim production readiness, deployment automation, or advanced AI capability unless implementation and validation exist.
