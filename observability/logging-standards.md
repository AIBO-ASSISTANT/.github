# Logging Standards

## Current Backend Baseline

The backend has structured logging, request IDs, event normalization, and redaction utilities.

## Required Fields

- timestamp
- level
- message
- request ID when request scoped
- event category/action/name when domain relevant
- status code and duration for HTTP requests
- safe context identifiers

## Rules

- Do not log secrets.
- Prefer structured fields over free-form string details.
- Use request IDs for cross-log correlation.
- Log dependency failures with category and safe context.
- Keep 4xx logs useful without treating all client errors as system failures.

## Future Work

- central log aggregation
- retention policy
- dashboard links
- alert integration for high-severity patterns
