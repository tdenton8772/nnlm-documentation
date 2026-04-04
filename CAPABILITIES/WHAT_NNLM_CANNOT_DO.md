# What NNLM Cannot Do

NNLM is deliberately constrained by design.
Those constraints are what make it grounded, auditable, and trustworthy.

This document describes **systemic non-capabilities** — not temporary limitations, missing features, or future roadmap items.

If a problem fundamentally requires any of the following, **NNLM is not the right tool**.

---

## 1. Creative or Open-Ended Generation

NNLM does not:

* write fiction
* compose poetry
* brainstorm ideas
* generate content without evidence

There is no concept of:

* "imagine something"
* "what if"
* "write me a story about"

NNLM generates from evidence, not from imagination.

If a use case requires unconstrained creativity, NNLM will feel rigid — because it is.

---

## 2. Knowledge Beyond NAM

NNLM does not have access to:

* the internet
* real-time data feeds
* knowledge not ingested into NAM
* its own training data as a knowledge source

If a fact is not in NAM, NNLM cannot produce it.

This is a feature. It means the system only says what it can prove.

But it also means:

* NNLM's knowledge is bounded by what has been ingested
* questions about topics not in NAM will produce rejections
* the system is only as complete as its NAM instance

---

## 3. Real-Time Learning

NNLM does not learn from user interactions.

At query time:

* no models are updated
* no weights are adjusted
* no feedback is incorporated
* no behavior changes

All training happens offline. Models are frozen at deployment.

If a use case requires:

* adaptive behavior
* learning from corrections
* self-improvement during operation

NNLM is not designed for that role.

---

## 4. General Conversation

NNLM is not a chatbot.

It does not:

* engage in small talk
* express opinions
* role-play
* provide emotional support
* handle requests outside its knowledge domain

NNLM answers questions from evidence. That is its scope.

Attempting to use it as a general conversational agent will produce unsatisfying results — intentionally.

---

## 5. Summarization Without Evidence

NNLM does not:

* summarize documents it has not retrieved
* condense information from its training data
* produce executive summaries from thin air

If asked to summarize, NNLM can only summarize what it has retrieved and selected — with citations.

Open-ended summarization of arbitrary text is not a capability.

---

## 6. Multi-Language Support

NNLM currently operates in English only.

It does not:

* translate between languages
* process queries in other languages
* generate responses in languages other than English

Multi-language support is a potential future direction, not a current capability.

---

## 7. Sub-Second Response Times

NNLM's multi-stage pipeline introduces latency at each stage.

On current infrastructure:

* end-to-end responses take several seconds
* the generation stage is the primary latency contributor
* CPU-based inference is slower than GPU-based inference

If a use case requires:

* real-time responses
* sub-second latency
* high-throughput streaming

Current NNLM infrastructure may not meet those requirements.

---

## 8. Autonomous Action

NNLM does not:

* execute tasks
* trigger workflows
* make decisions
* take actions in external systems

It produces language — specifically, cited responses from evidence.

NNLM can inform decisions. It does not make them.

---

## Summary

NNLM does **not** try to be:

* a general-purpose conversational AI
* a creative writing tool
* an autonomous agent
* a real-time system
* a multilingual platform

NNLM exists to do one thing well:

> **Produce grounded, cited natural language responses from deterministic memory.**

If a problem fits that shape, NNLM excels.
If it does not, NNLM will resist — and should.

-> See also: [What NNLM Can Do](WHAT_NNLM_CAN_DO.md) | [Current Limitations](CURRENT_LIMITATIONS.md) | [Design Principles](../PHILOSOPHY/DESIGN_PRINCIPLES.md)
