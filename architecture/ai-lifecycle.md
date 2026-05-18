# AI Lifecycle

The current AI layer is deterministic, not provider-backed LLM intelligence.

```mermaid
sequenceDiagram
  participant Backend
  participant EngineTransport as Engine FastAPI
  participant EngineDomain as Engine domain
  Backend->>EngineTransport: POST /ai-engine/analyze { text, reference_date }
  EngineTransport->>EngineDomain: preprocess -> classify -> extract
  EngineDomain-->>EngineTransport: classification, entities, warnings
  EngineTransport-->>Backend: analysis response
  Backend->>Backend: validate and optionally review analysis
  Backend->>EngineTransport: POST /decision-engine/decide { classification, entities, context }
  EngineTransport->>EngineDomain: route -> classify task type
  EngineDomain-->>EngineTransport: task_type, actions, warnings
  EngineTransport-->>Backend: decision response
  Backend->>Backend: validate, confirm, and execute actions
```

## Current Capabilities

- Supported intents include task creation, scheduling, project, and unknown.
- Engine returns confidence between `0` and `1`.
- Unknown or ambiguous input should result in `clarification.request` actions.
- Backend validates analysis and decision response shapes before using them.

## Future AI Requirements

Any future LLM or external AI provider must include:

- ADR approval.
- provider isolation behind engine contracts.
- evaluation data and regression thresholds.
- confidence and fallback policy.
- observability for latency, error rate, and classification distribution.
- redaction rules for prompts and logs.
- explicit user confirmation for risky mutations.
