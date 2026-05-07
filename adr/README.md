# Architecture Decision Records

ADRs record important technical decisions, tradeoffs, and consequences.

## When to Create an ADR

Create an ADR for changes that affect:

- service boundaries
- database ownership
- auth/session strategy
- deployment architecture
- observability architecture
- external AI/model providers
- cross-repository API contracts
- major dependency or infrastructure choices

## File Naming

```text
NNNN-short-title.md
```

Examples:

```text
0001-backend-owns-session-lifecycle.md
0002-engine-remains-transport-isolated.md
```

## Status Values

- `Proposed`
- `Accepted`
- `Superseded`
- `Rejected`

Use [0000-template.md](0000-template.md).
