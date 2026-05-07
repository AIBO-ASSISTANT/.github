# Entity Extraction Flow

Entity extraction is part of the richer engine pipeline, not the current backend `/intents/classify` response.

## Current Extracted Fields

The engine pipeline can produce fields such as:

- title
- date
- time
- duration
- priority
- project name
- participants
- unresolved fields
- raw extracted values

## Flow

```mermaid
flowchart LR
  Input[Normalized input] --> Classifier[Intent classifier]
  Classifier --> Extractor[Entity extractor]
  Extractor --> Router[Decision router]
  Router --> Output[Actions + warnings]
```

## Rules

- Relative dates require a reference date for reliable resolution.
- Unresolved fields must be surfaced instead of guessed.
- Raw extraction values are diagnostic and must not be treated as final persistence data without validation.
- Backend execution must use normalized action payloads, not raw extracted text.
