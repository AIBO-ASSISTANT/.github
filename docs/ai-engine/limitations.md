# Cognitive AI Boundaries & System Limitations

This document provides transparent, well-reasoned boundaries and operational limitations for `AIBO-ENGINE-V1.0`.

---

## 1. Safety & Behavioral Invariants

1. **Non-Autonomous High-Risk Mutations**: The engine is explicitly prohibited from autonomously executing destructive actions (e.g., deleting tasks, removing project boards, cancelling meetings). High-risk operations **always** require explicit, server-held confirmation tokens.
2. **Zero Direct Persistence Access**: The cognitive engine has zero direct database driver access to MongoDB or Redis. All world-state queries and mutations occur via authenticated backend client callbacks.
3. **Truthful Outcome Reporting**: If a scheduled slot is unavailable (HTTP 409) or an action fails validation, the engine must report the conflict truthfully; it cannot claim success when an action was blocked.

---

## 2. Technical Limitations

| Boundary Area | Limitation Description | Mitigation in V1.0 |
| :--- | :--- | :--- |
| **Provider Network Latency** | External LLM API calls (Gemini/OpenAI) can suffer network jitter. | Local offline Ollama (`qwen2.5:7b`) fallback, monotonic deadline clamping, and in-memory mock provider for testing. |
| **Scope of Calendar Awareness** | Conflict detection evaluates only schedules stored inside AIBO. External calendar feeds (Google / Outlook) are not yet synchronized. | Bi-directional calendar sync is planned for release V1.2. |
| **Temporal Date Context** | Relative temporal expressions ("tomorrow", "in 2 days") require a valid anchor date. | Backend always passes client `reference_date`, deterministically normalized via `date_resolver.py`. |
| **Stateless Model Core** | The engine does not perform in-flight model fine-tuning or weight modification. | Long-term memory is persisted as structured semantic memory documents in MongoDB. |
| **Single-Turn Confirmation Window** | Pending confirmation tokens expire after a monotonic TTL (default 300s). | Expired tokens fail closed; the assistant prompts the user to re-initiate the action. |
