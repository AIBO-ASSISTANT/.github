# API Governance

API governance applies to `AIBO-BACKEND` and any future public service boundary.

## Canonical Source

The current OpenAPI source is:

```text
AIBO-BACKEND/docs/openapi.yaml
```

This governance directory defines how that file should be maintained.

## Required Docs

- [OpenAPI standards](openapi-standards.md)
- [Endpoint naming](endpoint-naming.md)
- [Versioning and deprecation](versioning-deprecation.md)
- [Validation and errors](validation-errors.md)

## API Principles

- Contract changes are product changes.
- Backward-incompatible changes require explicit migration notes.
- Error formats must be predictable.
- API docs must not list routes that do not exist.
- Implemented routes must not remain undocumented once they are intended for frontend or external use.
