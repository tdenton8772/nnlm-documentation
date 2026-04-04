# Glossary

This glossary is a **navigation aid**, not an authoritative definitions document.

Canonical definitions for NNLM concepts live in
**PHILOSOPHY/TERMINOLOGY.md**.
This file exists to help readers quickly orient themselves and find the
correct place to learn more.

---

## Address Projection

The process by which the encoder transforms a natural language query into
structured NAM query instructions — mapping from language to semantic
address space.

-> See: **PHILOSOPHY/TERMINOLOGY.md -- Address Projection**

---

## Affordance

An inferred capability of a query that determines *how* NAM should interpret
and explore knowledge (e.g., identity lookup, location, relationship).

Affordances guide retrieval strategy but do not score or rank results.

-> See: **PHILOSOPHY/TERMINOLOGY.md -- Affordance**

---

## Citation

A structural marker in generated output that links a specific factual claim
to the retrieved record that supports it. Citations are produced by the
generation model as first-class output tokens, not added by post-processing.

-> See: **PHILOSOPHY/TERMINOLOGY.md -- Citation**

---

## Encoder

The first stage of the NNLM pipeline. Transforms a natural language query
into a structured query plan, including entity resolution, intent detection,
and NAM query parameters.

-> See: **ARCHITECTURE/PIPELINE_MODEL.md -- Stage 1: Encoding**

---

## Entity Resolution

The process of identifying and normalizing entities mentioned in a query —
recognizing that different surface forms refer to the same entity, resolving
pronouns, and mapping to canonical identifiers.

-> See: **PHILOSOPHY/TERMINOLOGY.md -- Entity Resolution**

---

## Generation

The fourth and final stage of the NNLM pipeline. Produces a natural language
response with citations from the selected records, or a rejection when evidence
is insufficient.

-> See: **ARCHITECTURE/GENERATION_MODEL.md**

---

## Grounding

The structural connection between a generated claim and a retrieved record.
In NNLM, grounding is architectural — enforced by the pipeline, not suggested
by prompting.

-> See: **PHILOSOPHY/TERMINOLOGY.md -- Grounding**

---

## Match Geometry

The structural relationship between a query and a retrieved record in NAM's
semantic space. Records may match as points (exact), lines (partial), or
planes (broad).

-> See: **PHILOSOPHY/TERMINOLOGY.md -- Match Geometry**

---

## Memory Substrate

The underlying system that stores, organizes, and retrieves knowledge. In NNLM,
the memory substrate is NAM. All knowledge lives in NAM and is accessed through
deterministic retrieval.

-> See: **PHILOSOPHY/TERMINOLOGY.md -- Memory Substrate**

---

## Noise Record

A record returned by NAM retrieval that is not relevant to the user's query.
Noise records are expected (NAM retrieval is broad by design) and are removed
by the record selection stage before generation.

-> See: **PHILOSOPHY/TERMINOLOGY.md -- Noise Record**

---

## Ontology

A semantic role category used by NAM to organize knowledge (e.g., person,
location, event, organization). Ontologies define what kind of thing a record
describes, influencing retrieval and selection.

-> See: **PHILOSOPHY/TERMINOLOGY.md -- Ontology**

---

## Query Plan

The structured output of the encoder stage. Describes what entities were
identified, what retrieval strategy should be used, and what NAM parameters
to apply.

-> See: **PHILOSOPHY/TERMINOLOGY.md -- Query Plan**

---

## Record Selection

The third stage of the NNLM pipeline. Separates signal from noise by scoring
each retrieved record for relevance using NAM metadata. The gatekeeper between
retrieval and generation.

-> See: **ARCHITECTURE/RECORD_SELECTION.md**

---

## Rejection

The system's decision to decline answering a query when record selection
determines that no retrieved records sufficiently address the question.
A first-class output, not an error.

-> See: **PHILOSOPHY/TERMINOLOGY.md -- Rejection**

---

## Retrieval

The second stage of the NNLM pipeline. Executes the query plan against NAM
and returns candidate records with rich metadata. Deterministic — the same
query plan always produces the same results.

-> See: **ARCHITECTURE/PIPELINE_MODEL.md -- Stage 2: Retrieval**

---

## Session

A conversation context that tracks discussed entities, resolved references,
and topic continuity across multiple turns. Managed through structured state,
not context window stuffing.

-> See: **ARCHITECTURE/SESSION_AND_CONTEXT.md**

---

## Soft Prompt

A learned embedding injected into the generation model's input that carries
structured information from the record selection stage — relevance signals,
confidence indicators — richer than text alone.

-> See: **PHILOSOPHY/TERMINOLOGY.md -- Soft Prompt**

---

### Notes

* This glossary intentionally avoids implementation detail.
* If a term appears ambiguous or overloaded, defer to **TERMINOLOGY.md**.
* New terms should be added here *only after* being defined canonically.

-> See also: [Terminology](../PHILOSOPHY/TERMINOLOGY.md) (canonical definitions)
