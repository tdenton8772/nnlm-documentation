# What NNLM Can Do

NNLM is designed to produce **grounded, cited natural language responses** from deterministic memory.

It excels in domains where *accuracy*, *traceability*, and *honesty* matter more than fluency or creativity.

This document describes the **classes of problems NNLM is explicitly built to solve**.

---

## 1. Grounded Question Answering With Citations

NNLM answers questions by retrieving evidence from NAM and generating responses that cite their sources.

Every factual claim in a response is:

* tied to a specific retrieved record
* marked with a citation
* verifiable against the evidence

This enables:

* auditable responses
* trust in generated output
* accountability for every claim

NNLM is especially effective where:

* "the AI said so" is not an acceptable justification
* responses must withstand scrutiny
* correctness matters more than speed

---

## 2. Multi-Turn Conversation With Session Memory

NNLM supports multi-turn conversations through session-based context.

Across turns, the system:

* tracks discussed entities
* resolves pronouns and coreferences
* maintains topic continuity
* produces progressively more focused queries

This happens through structured session memory — not by re-processing the entire conversation history in a context window.

---

## 3. Noise Rejection and Mismatch Detection

Not every query has a good answer in the knowledge base.

NNLM handles this honestly:

* the record selection stage identifies when retrieved records do not actually address the query
* the system produces a rejection response rather than hallucinating
* rejection is a measurable, first-class output

This means users receive honest signals about knowledge gaps.

NNLM will not invent an answer to avoid silence.

---

## 4. Exploratory Querying

NNLM supports queries that are broad or underspecified.

When a query does not strongly target a specific answer:

* NAM retrieves from broader regions of semantic space
* selection still filters noise
* the response reflects what is known without overcommitting to a single interpretation

This makes NNLM suitable for:

* investigating a topic
* understanding what the knowledge base contains
* asking "What do we know about X?"

Exploration in NNLM is deterministic — the same broad query always produces the same response.

---

## 5. Deterministic, Auditable Responses

Given the same query and the same NAM state:

* the same records are retrieved
* the same selection decisions are made
* the same evidence base reaches the generation model

The retrieval and selection stages are fully deterministic.

This enables:

* reproducibility
* regression testing
* forensic analysis of responses
* compliance in regulated environments

The generation stage has controlled variability in phrasing, but the factual content and citations remain stable.

---

## 6. Per-Stage Latency Measurement

Every stage of the NNLM pipeline produces measurable timing data:

* encoding time
* NAM retrieval time
* record selection time
* generation time

This enables:

* identifying bottlenecks
* optimizing specific stages
* setting realistic latency expectations
* tracking performance over time

Observability is not an afterthought — it is a design consequence of the multi-stage architecture.

---

## 7. Query Plan Inspection

The encoder's output — the query plan — is fully inspectable.

Users and operators can see:

* what entities were identified
* how the query was interpreted
* what retrieval strategy was chosen
* how pronouns and coreferences were resolved

This transparency enables:

* debugging unexpected responses
* understanding why specific records were retrieved
* improving the encoder based on observed query plans

---

## 8. Knowledge Gap Identification

When NNLM rejects a query, the rejection is informative.

It indicates:

* the system searched NAM but found insufficient evidence
* specific retrieval regions were explored without adequate results
* the knowledge base has a gap in this area

This turns rejected queries into **actionable signals** for knowledge base improvement.

---

## Summary

NNLM is well suited for problems that require:

* **grounded responses** with citations
* **honest behavior** including rejection
* **deterministic, reproducible** query processing
* **multi-turn conversation** with session context
* **observable, debuggable** pipeline behavior
* **auditable** response chains from query to evidence to claim

It is not a general-purpose conversational AI.
It is a **structurally grounded language interface** to deterministic memory.

-> See also: [What NNLM Cannot Do](WHAT_NNLM_CANNOT_DO.md) | [Current Limitations](CURRENT_LIMITATIONS.md) | [Grounding Model](../ARCHITECTURE/GROUNDING_MODEL.md)
