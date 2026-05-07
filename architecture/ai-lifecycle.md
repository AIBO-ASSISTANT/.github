# AI Lifecycle

The current AI layer is deterministic, not provider-backed LLM intelligence.

```mermaid
sequenceDiagram
  participant Backend
  participant EngineTransport as Engine /classify
  participant EngineDomain as Engine domain
  Backend->>EngineTransport: POST /classify { input }
  EngineTransport->>EngineDomain: classify_text(input)
  EngineDomain-->>EngineTransport: intent, confidence, processed_input
  EngineTransport-->>Backend: classification response
  Backend->>Backend: validate engine response
  Backend-->>Backend: use classification result or return service error
```

## Current Capabilities

- Supported intents include task creation, scheduling, project, and unknown.
- Engine returns confidence between `0` and `1`.
- Unknown or ambiguous input should result in clarification behavior in the richer pipeline.
- Backend validates engine response shape before returning classification.

## Future AI Requirements

Any future LLM or external AI provider must include:

- ADR approval.
- provider isolation behind engine contracts.
- evaluation data and regression thresholds.
- confidence and fallback policy.
- observability for latency, error rate, and classification distribution.
- redaction rules for prompts and logs.
- explicit user confirmation for risky mutations.
