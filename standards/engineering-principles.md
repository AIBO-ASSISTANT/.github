# Engineering Principles

## Principles

- Accuracy beats ambition. Do not describe planned systems as implemented.
- Interfaces should be explicit, validated, and documented.
- Service ownership should be clear enough that a bug has an obvious owner.
- Security and observability are product requirements, not polish.
- The simplest design that preserves correctness is preferred.
- Tests should match risk and contract importance.
- Documentation should reduce ambiguity, not increase ceremony.

## Decision Priorities

When tradeoffs conflict, prefer:

1. user data safety
2. security and access control
3. contract correctness
4. maintainability
5. operability
6. delivery speed
7. convenience

## Engineering Review Questions

- What contract does this change affect?
- What data can be exposed, lost, corrupted, or duplicated?
- What happens when this dependency is unavailable?
- How is the failure observed?
- Which docs become wrong if this merges?
