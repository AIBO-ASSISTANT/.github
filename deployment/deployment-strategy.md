# Deployment Strategy

## Current State

No production deployment target, hosting provider, environment inventory, or automated deploy workflow is implemented.

## Strategy

1. Stabilize CI in all service repositories.
2. Define environments.
3. Define secrets management.
4. Add health checks.
5. Add release approval rules.
6. Add deployment automation.
7. Add rollback validation.

## Required Deployment Units

- frontend web application
- backend API service
- engine service
- MongoDB
- PostgreSQL

## Deployment Workflow Placeholder

Deployment workflows may be committed as disabled or manual placeholders only when they clearly state that they do not deploy production.
