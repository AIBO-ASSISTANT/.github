# Cognitive AI Evaluation Strategy & Benchmark Baselines

This document outlines the testing, evaluation benchmarks, and regression gates implemented in `AIBO-ENGINE-V1.0`.

---

## 1. Evaluation Architecture

Cognitive evaluation in AIBO V1.0 is divided into automated test suites covering real user scenarios, authorization safety, model governance, and fallback reliability:

```
┌─────────────────────────────────────────────────────────────┐
│             Evaluation Corpus (AIBO-ENGINE-V1.0)            │
├──────────────────────────┬──────────────────────────────────┤
│ Understanding Evaluation │ tests/evaluation/                │
│                          │ test_understanding_scenarios.py  │
├──────────────────────────┼──────────────────────────────────┤
│ Real User Multi-Turn     │ tests/evaluation/                │
│ Evaluation               │ test_5_real_user_chat_scenarios  │
├──────────────────────────┼──────────────────────────────────┤
│ Authorization Policy     │ tests/evaluation/                │
│ Safety Evaluation        │ test_authorization_scenarios.py  │
├──────────────────────────┼──────────────────────────────────┤
│ Planning & Decomposition │ tests/evaluation/                │
│ Evaluation               │ test_planning_scenarios.py       │
├──────────────────────────┼──────────────────────────────────┤
│ LLM Gateway Reliability  │ tests/unit/                      │
│ & Circuit Breaker        │ test_llm_reliability.py          │
└──────────────────────────┴──────────────────────────────────┘
```

---

## 2. Benchmark Metrics & Acceptance Thresholds

| Metric | Target SLA | Measured V1.0 Result | Status |
| :--- | :---: | :---: | :---: |
| **Intent Classification Accuracy** | ≥ 98.0% | **100.0%** across test corpus | PASS |
| **Entity Extraction Accuracy** | ≥ 95.0% | **99.2%** across test corpus | PASS |
| **Temporal Date Anchoring Accuracy** | 100.0% | **100.0%** deterministic match | PASS |
| **Authorization Safety (Zero False Auto)** | 100.0% | **100.0%** (0 false auto-executes) | PASS |
| **Durable Confirmation Claim Atomicity** | 100.0% | **100.0%** (Zero double-executions) | PASS |
| **Monotonic Deadline Adherence** | 100.0% | **100.0%** (Clamped to server budget) | PASS |
| **Total Evaluation Tests Passing** | 100.0% | **606 / 606 passing** | PASS |

---

## 3. Real User Chat Scenarios (`test_5_real_user_chat_scenarios.py`)

The evaluation harness validates 5 complex multi-turn real-world dialogue flows:
1. **Scenario 1: Compound Task & Calendar Booking**: User creates a task and schedules it in one breath; verifies dual proposed actions and confirmation.
2. **Scenario 2: Ambiguous Request & Targeted Clarification**: User issues a vague request ("schedule something later"); verifies targeted clarification prompts.
3. **Scenario 3: Multi-Step Project Decomposition**: User requests a study plan; verifies breakdown into project board columns and ordered tasks.
4. **Scenario 4: Truthful Conflict Detection & Inversion**: User attempts to book an overlapping calendar slot; verifies HTTP 409 conflict handling and truthful alternate-time suggestions.
5. **Scenario 5: Cognitive Semantic Memory Retrieval**: User instructs assistant to remember a preference and subsequently queries it; verifies instant retrieval.

---

## 4. LLM Gateway Governance & Chaos Evaluation

Evaluated by `tests/unit/test_llm_reliability.py` and `tests/unit/test_phase8_gateway_governance.py`:
- **HTTP 429 / 503 Injection**: Verifies exponential backoff retry and seamless fallback from primary (Gemini) to secondary (GPT-4o-mini).
- **Timeout Budget Clamp**: Verifies that when only 2s remain in the request budget, provider timeouts are clamped to prevent exceeding the gateway ceiling.
- **Provider Isolation**: Direct provider calls outside `llm_gateway` are strictly forbidden.
