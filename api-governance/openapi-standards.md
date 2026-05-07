# OpenAPI Standards

## Required Metadata

OpenAPI documents must include:

- title
- version
- server list
- tags
- operation IDs
- request schema
- success response schema
- error response references
- auth requirements

## Operation Rules

- `operationId` must be stable and unique.
- Route summaries should describe behavior, not implementation.
- Security requirements must match actual middleware.
- Request examples must avoid secrets and private data.
- Response examples must use the standard envelope.

## Schema Rules

- Reuse components for repeated response envelopes.
- Define enums where values are constrained.
- Document nullable fields explicitly.
- Keep naming consistent with runtime JSON.

## Drift Rule

If an implemented route changes request, response, auth, status code, or error behavior, OpenAPI must be updated in the same pull request.
