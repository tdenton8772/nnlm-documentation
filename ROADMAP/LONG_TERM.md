# Long-Term Direction

### Where NNLM is headed — not as a feature list, but as an evolution of capability and trust

---

## Purpose of the Long-Term Roadmap

This document describes **where NNLM is intended to go**, not as a checklist of features, but as a statement of directional intent.

Nothing in this section is a commitment.
Everything here reflects **directional intent**, constrained by NNLM's core invariants:

* grounding over fluency
* citation over assertion
* rejection over hallucination
* determinism over creativity

---

## 1. Domain-Specific Fine-Tuning Pipeline

Long-term, NNLM should support **repeatable adaptation to new domains** without architectural changes.

This means:

* a defined process for training record selection on domain-specific data
* a defined process for training generation on domain-specific citation patterns
* tooling that makes domain adaptation a workflow, not a research project
* artifact management that versions and isolates domain-specific models

The goal is not universal generality — it is **controlled specificity** across many domains.

---

## 2. Multi-Language Support

NNLM currently operates in English only.

Long-term directions include:

* query processing in additional languages
* generation in multiple languages
* cross-language retrieval (query in one language, retrieve from another)
* language-specific entity resolution and coreference

Multi-language support must preserve NNLM's grounding guarantees.
Adding a language is not just translation — it requires ensuring that citation, rejection, and determinism work correctly in the new language.

---

## 3. Streaming Generation

Current generation produces a complete response before returning it.

Streaming generation would:

* begin delivering tokens to the user as they are produced
* reduce perceived latency significantly
* require maintaining citation integrity in a streaming context
* require careful handling of rejection — a stream cannot be "unsaid"

Streaming must not compromise grounding. A streamed token that is later contradicted by evidence is worse than waiting for a complete, correct response.

This tension — responsiveness vs. integrity — will define the implementation approach.

---

## 4. Federated Deployment Across NAM Clusters

NNLM currently connects to a single NAM cluster.

Long-term, federated deployment would enable:

* querying across multiple NAM instances
* domain-specific NAM clusters with a unified NNLM interface
* cross-organization knowledge access with appropriate boundaries
* distributed evidence gathering with centralized generation

Federation introduces complexity — especially around record selection when evidence comes from multiple sources.

---

## 5. API-First Access for External Consumers

NNLM currently serves through its web interface.

Long-term, a stable API would enable:

* programmatic access to NNLM's pipeline
* integration into external applications and workflows
* per-stage access (not just end-to-end queries)
* authenticated, rate-limited access for external consumers

The API must expose NNLM's observability — not just the final response, but the query plan, retrieval metadata, and selection decisions.

Opacity defeats the purpose of the architecture.

---

## 6. Improved Rejection Intelligence

Current rejection is binary — either enough records pass selection or they do not.

Long-term directions include:

* more nuanced rejection signals (partial evidence, adjacent topics, suggested reformulations)
* confidence gradients rather than binary thresholds
* informative rejection responses that help users understand what the system does and does not know

Rejection should become more helpful without becoming less honest.

---

## 7. Compounding Value Over Time

The most important long-term property of NNLM is **compounding trust**.

As the system matures:

* more training data improves selection and generation
* more NAM coverage improves retrieval
* more operational experience improves reliability
* more domain adaptations prove the architecture

This compounding effect is independent of any single model improvement.

NNLM is designed to still matter when:

* today's language models are replaced by better ones
* today's training techniques are superseded
* today's infrastructure choices are outdated

The architecture survives model changes. The models serve the architecture, not the other way around.

---

## What This Is Not

This roadmap is intentionally **not**:

* a feature wishlist
* a promise of timelines
* a claim of inevitability

It is a statement of **direction**, grounded in the belief that:

> Grounded, cited, honest language generation is worth building infrastructure for.

-> See also: [Current State](CURRENT_STATE.md) | [Near-Term Roadmap](NEAR_TERM.md) | [Design Principles](../PHILOSOPHY/DESIGN_PRINCIPLES.md)
