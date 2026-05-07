# AI Evaluation Strategy

The engine needs an evaluation harness before AI capability expansion.

## Required Evaluation Assets

- Versioned input corpus.
- Expected intent labels.
- Expected entity outputs for supported workflows.
- Clarification examples.
- Regression thresholds.
- Error categorization.

## Metrics to Track

- intent accuracy by class
- unknown/clarification rate
- entity extraction accuracy
- action routing correctness
- latency
- invalid response rate

## Future Provider Evaluation

Before introducing an LLM or external AI provider:

- compare against deterministic baseline
- define failure fallback
- test prompt/model version changes
- review privacy and data retention
- define logging redaction
- document cost and latency impact
- create an ADR
