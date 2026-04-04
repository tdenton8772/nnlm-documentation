# Current Limitations

This document describes the **current, observable limitations** of NNLM as it exists today.
These are not philosophical constraints or permanent non-goals — those are covered in [What NNLM Cannot Do](WHAT_NNLM_CANNOT_DO.md).

Everything here is about **maturity, scope, and operational reality**.

---

## 1. End-to-End Latency

NNLM's multi-stage pipeline introduces cumulative latency.

Current performance on CPU infrastructure:

* end-to-end response time: approximately 5-6 seconds
* the generation stage is the primary contributor
* encoding and NAM retrieval are comparatively fast
* record selection adds minimal overhead

This latency is acceptable for many use cases but is not suitable for real-time or interactive-speed applications.

GPU inference for the generation stage is expected to reduce this significantly.

---

## 2. Training Corpus Size

The current models are trained on a corpus of approximately 31,000 examples.

This means:

* the system performs well within the domains represented in training data
* edge cases and unusual query patterns may not be handled optimally
* record selection thresholds are calibrated to the training distribution
* larger and more diverse training corpora will improve generalization

The corpus size reflects the current stage of development, not a design ceiling.

---

## 3. Single Language

NNLM currently operates in English only.

There is no support for:

* query processing in other languages
* multilingual retrieval
* cross-language generation

This is a scope constraint of the current implementation.

---

## 4. No GPU Inference Deployed

The generation stage currently runs on CPU.

This means:

* inference is slower than it would be on GPU
* per-token generation speed is the primary bottleneck
* scaling generation throughput requires more compute, not just more instances

GPU deployment is a near-term priority that is expected to bring latency below one second.

---

## 5. Fixed Record Selection Threshold

The record selection threshold — which determines how aggressively noise is filtered — is currently fixed at deployment time.

This means:

* the threshold cannot be adjusted per query
* different query types receive the same filtering aggressiveness
* tuning requires redeployment, not runtime configuration

A fixed threshold provides predictability and determinism.
A more adaptive approach may be explored in the future.

---

## 6. Session Memory Scope

Session memory is limited to the current NAM cluster's data.

This means:

* multi-turn conversations operate within the knowledge available in the connected NAM instance
* there is no cross-cluster session context
* session state does not persist between disconnected conversations

Session memory enhances query planning within a conversation. It does not expand the underlying knowledge base.

---

## 7. Encoder Robustness

The encoder stage handles a wide range of natural language queries, but:

* highly ambiguous queries may produce suboptimal query plans
* unusual entity references may not resolve correctly
* very short or very long queries may challenge the encoding pipeline

The encoder improves with exposure to more diverse query patterns during training.

---

## 8. Observability Tooling

Per-stage measurement is available, but:

* external monitoring integrations are not yet connected
* alerting on pipeline failures is not automated
* performance dashboards are functional but still maturing

Observability exists at the data level. Operational tooling around it is still developing.

---

## 9. Single-Threaded Development

Practically speaking:

* development velocity is constrained by team size
* features are prioritized carefully
* correctness is favored over speed of expansion

This is a reality of stage, not a reflection of ambition.

---

## What Does Work Well Today

Despite these limitations, NNLM today supports:

* end-to-end grounded question answering with citations
* multi-turn conversation with session-based pronoun and entity resolution
* noise record filtering via trained record selection
* rejection when evidence is insufficient
* deterministic retrieval and selection behavior
* query plan inspection for debugging
* per-stage latency measurement
* exploratory and targeted query modes
* a functional web interface with query plan inspector

These are real, working capabilities — not prototypes.

---

## How to Read This Document

If you are:

* **evaluating NNLM** — treat this as a candid readiness assessment
* **building with NNLM** — use this to scope expectations realistically
* **comparing NNLM to alternatives** — use this to understand current trade-offs

None of these limitations contradict the core design principles.
They simply reflect where the system is today.

-> See also: [What NNLM Can Do](WHAT_NNLM_CAN_DO.md) | [What NNLM Cannot Do](WHAT_NNLM_CANNOT_DO.md) | [Current State](../ROADMAP/CURRENT_STATE.md)
