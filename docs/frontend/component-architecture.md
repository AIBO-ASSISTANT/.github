# Frontend Component Architecture

`AIBO-FRONTEND` uses a feature-oriented React structure.

## Structure Rules

- `src/app` owns application composition, routing, providers, layouts, and app-level boundaries.
- `src/features/<feature>` owns feature-specific pages, services, hooks, context, and components.
- `src/shared` owns reusable primitives that do not depend on one domain.
- `src/config` owns environment parsing.
- `src/styles` owns global styles and design variables.

## Component Rules

- Components should receive data through props, hooks, or feature context.
- API calls belong in service modules, not directly in render components.
- Feature components should not import from sibling feature internals unless a shared abstraction exists.
- Shared components must be domain-neutral.
- Error and loading states must be explicit.

## Current Maturity

The frontend has scaffolding, auth lifecycle helpers, route protection, login/signup pages, admin login page, dashboard route, and tests for auth helpers. Complete task, schedule, project, and chatbot product screens are not implemented.
