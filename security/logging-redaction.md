# Logging and Redaction Policy

## Sensitive Values

Logs must redact:

- passwords
- access tokens
- refresh tokens
- cookies
- authorization headers
- set-cookie headers
- refresh token hashes and IDs
- database credentials
- provider API keys
- private user content unless explicitly approved for diagnostics

## Required Log Fields

Backend logs should preserve:

- request ID
- event category/action/name
- route or URL
- status code
- duration
- safe user or entity identifiers when useful
- sanitized error context

## Rules

- Do not log full request bodies by default.
- Do not log raw headers by default.
- Use structured logs instead of string concatenation for operational events.
- Security denials should be observable without revealing exploit details.
- Development logs may be more readable, but not less redacted.
