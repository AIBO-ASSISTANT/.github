# Engine Setup

From `AIBO-ENGINE`:

```bash
python api.py
```

Run tests:

```bash
python -m unittest discover -s tests
```

Run CLI processing:

```bash
python main.py "Create a high priority task to submit report tomorrow" --reference-date 2026-05-03
```

## Current Contract

The transport exposes:

```text
POST /classify
```

The engine is deterministic and rule-based. Do not treat it as an LLM-backed system.

## Setup Gap

Add a dependency manifest before relying on automated CI installation for engine dependencies.
