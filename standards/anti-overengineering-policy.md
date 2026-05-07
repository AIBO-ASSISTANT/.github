# Anti-Overengineering Policy

## Policy

AIBO should be built for production maturity without pretending every enterprise pattern is needed on day one.

## Add Complexity When

- there is a measured reliability, security, scale, or maintainability need
- the team can operate the added component
- failure modes are understood
- documentation and ownership are clear
- rollback is possible

## Avoid

- queues without asynchronous workflows
- caching without measured read pressure and invalidation rules
- microservices without ownership and deployment maturity
- LLM providers without evaluation and safety gates
- generic abstraction layers with one implementation
- deployment automation before environments and secrets are defined

## Review Test

If a change cannot explain what risk it reduces or what current pain it removes, it probably should not be added yet.
