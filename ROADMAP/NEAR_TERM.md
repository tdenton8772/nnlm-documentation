# Near-Term Roadmap

**Time horizon:** ~3-6 months
**Scope:** Hardening NNLM for reliability, speed, and broader knowledge coverage

---

## Purpose of the Near-Term Roadmap

The near-term roadmap focuses on making NNLM **faster, more capable, and more robust** without changing its fundamental architecture.

This phase is not about adding new capabilities.
It is about **making what already works perform better and scale further**.

---

## 1. GPU Inference for Generation

**Goal:** Reduce end-to-end latency from ~5-6 seconds to sub-1 second.

The generation stage is the primary latency contributor. It currently runs on CPU.

Near-term efforts focus on:

* deploying the generation model on GPU infrastructure
* optimizing the inference path for throughput
* reducing per-token generation time

**Outcome:**
NNLM becomes viable for interactive use cases where multi-second latency is unacceptable.

---

## 2. Larger Training Corpus

**Goal:** Improve generalization and handling of diverse query patterns.

The current models are trained on approximately 31,000 examples. Expanding the corpus will:

* improve record selection accuracy across more domains
* improve generation quality for edge cases
* improve entity resolution for less common entities
* provide more training signal for citation behavior

**Outcome:**
The system handles a wider range of queries with higher confidence.

---

## 3. Improved Record Selection With Graph Walk Integration

**Goal:** Use NAM's graph traversal capabilities to enrich the evidence available to record selection.

NAM supports graph walk queries that discover semantically related records across multiple dimensions. Integrating this into the NNLM pipeline would:

* expand the candidate record set beyond direct address matches
* provide selection with a richer evidence pool
* improve recall for queries where direct retrieval is narrow

**Outcome:**
Record selection operates on a more complete evidence set, improving both answer quality and rejection accuracy.

---

## 4. Export Pipeline for Optimization

**Goal:** Enable model export to optimized inference formats.

Near-term efforts include:

* establishing export pipelines for optimized model formats
* validating that exported models preserve citation behavior
* enabling deployment on different inference backends

**Outcome:**
The generation and selection models can be deployed on optimized infrastructure without retraining.

---

## 5. Better Session Handling

**Goal:** Improve multi-turn conversation reliability and context resolution.

Current session handling works but has areas for improvement:

* more robust pronoun resolution across longer conversations
* better handling of topic shifts within a session
* improved entity tracking when the same entity is referenced in different ways
* session state cleanup and management

**Outcome:**
Multi-turn conversations feel more natural and maintain coherence across more turns.

---

## 6. Expanded Observability

**Goal:** Connect per-stage measurement to external monitoring systems.

Current observability provides per-stage data. Near-term work includes:

* metrics export to monitoring platforms
* automated alerting on pipeline failures
* dashboards for operational monitoring
* performance trending over time

**Outcome:**
NNLM can be monitored and operated with standard infrastructure tooling.

---

## What This Phase Is Not

The near-term roadmap does **not** include:

* fundamentally new pipeline stages
* real-time learning or adaptive behavior
* multi-language support
* autonomous agent capabilities
* streaming generation

Those belong in later phases.

---

## Summary

The near-term roadmap is about **speed, depth, and reliability**:

* speed through GPU inference
* depth through larger training data
* reliability through better selection, session handling, and observability

Once these foundations are solid, expanding capability becomes much easier — and much safer.

-> See also: [Current State](CURRENT_STATE.md) | [Current Limitations](../CAPABILITIES/CURRENT_LIMITATIONS.md) | [Long-Term Direction](LONG_TERM.md)
