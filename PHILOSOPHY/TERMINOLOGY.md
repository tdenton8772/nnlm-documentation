# Terminology

*A shared language for reasoning about NNLM*

NNLM introduces vocabulary that overlaps with retrieval systems, language models, and machine learning — but **does not mean the same things**.

This document defines how key terms are used **specifically within NNLM**.
If a term is defined here, this definition takes precedence over industry norms.

---

## Grounding

**Grounding** is the structural connection between a generated claim and a retrieved record.

In NNLM, grounding is not:

* a prompting strategy
* a post-hoc verification step
* a quality metric

Grounding is:

* **architectural** — enforced by the pipeline
* **traceable** — every claim links to a specific record
* **binary** — a claim is either grounded or it is not produced

Grounding is the central guarantee of the system.

---

## Citation

A **citation** is a structural marker in generated output that identifies which retrieved record supports a specific claim.

Citations in NNLM are:

* produced by the generation model as part of its output
* not added by post-processing
* required for every factual assertion
* verifiable against the selected record set

A response without citations is treated as a system failure, not a stylistic choice.

---

## Noise Record

A **noise record** is a record returned by NAM retrieval that is not relevant to the user's query.

NAM retrieval is broad by design — it returns records that occupy compatible regions of semantic space. Not all of these records are useful for answering the specific question asked.

Noise records are:

* expected, not erroneous
* identified and filtered by the record selection stage
* never passed to the generation model

Removing noise before generation is critical to preventing hallucination.

---

## Record Selection

**Record selection** is the stage that separates signal from noise in retrieved records.

It takes the full set of records from NAM retrieval and produces a scored, filtered subset — the records that actually address the user's query.

Record selection is:

* a learned model, not a heuristic filter
* informed by NAM metadata (ontology, geometry, match scores)
* the gatekeeper between retrieval and generation
* the mechanism that enables rejection

---

## Soft Prompt

A **soft prompt** is a learned representation injected into the generation model's input, alongside or in place of text tokens.

In NNLM, soft prompts carry:

* learned signals from the record selection stage
* information about which records are relevant and how strongly
* structured conditioning that is richer than text alone

Soft prompts are not:

* text strings
* hand-crafted instructions
* prompt engineering

They are **learned embeddings** that condition the generation model on selection output.

---

## Address Projection

**Address projection** is the process by which the encoder transforms a natural language query into structured NAM query instructions.

This involves:

* identifying entities
* resolving references
* determining query intent
* producing coordinates and parameters for NAM retrieval

Address projection is the bridge between natural language and NAM's semantic address space.

---

## Memory Substrate

The **memory substrate** is the underlying system that stores, organizes, and retrieves knowledge.

In NNLM, the memory substrate is NAM.

NNLM does not maintain its own knowledge store.
It does not learn facts into weights.
All knowledge lives in NAM and is accessed through deterministic retrieval.

The memory substrate is not an accessory — it is the foundation.

---

## Rejection

**Rejection** is the system's decision to decline answering a query.

Rejection occurs when:

* record selection determines that no retrieved records sufficiently address the query
* the signal-to-noise ratio is below the acceptance threshold
* the system cannot ground a response in evidence

Rejection is:

* a first-class output, not an error
* preferable to hallucination
* actionable (it indicates a knowledge gap)

---

## Query Plan

A **query plan** is the structured output of the encoder stage.

It describes:

* what entities were identified
* what retrieval strategy should be used
* what NAM parameters to apply
* how the query was interpreted

Query plans are:

* inspectable
* deterministic for a given input
* the contract between the encoder and NAM

---

## Entity Resolution

**Entity resolution** is the process of identifying and normalizing entities mentioned in a query.

This includes:

* recognizing that "Tyler" and "Tyler Denton" refer to the same entity
* resolving pronouns in multi-turn conversation
* mapping surface forms to canonical identifiers

Entity resolution happens in the encoder stage and directly affects retrieval quality.

---

## Affordance

An **affordance** describes *what kind of question* is being asked.

Examples:

* identity ("Who is...?")
* location ("Where is...?")
* temporal ("When did...?")
* relationship ("How are X and Y connected?")

Affordances guide retrieval strategy in NAM.
They are detected during encoding.

---

## Ontology

An **ontology** in the NNLM context refers to the semantic role categories used by NAM to organize knowledge.

Examples:

* person
* location
* event
* organization

Ontologies define *what kind of thing* a record describes.
They influence retrieval, selection, and generation behavior.

---

## Match Geometry

**Match geometry** describes the structural relationship between a query and a retrieved record in NAM's semantic space.

A record may match as:

* a **point** — exact alignment on all semantic axes
* a **line** — alignment on some axes, broad on others
* a **plane** — broad alignment, exploratory match

Match geometry is part of the metadata returned by NAM and informs record selection.

---

## Why Terminology Matters

NNLM fails if teams use:

* "context" when they mean "retrieved records"
* "prompt" when they mean "soft prompt injection"
* "retrieval" when they mean "retrieval plus selection"
* "grounded" when the connection is aspirational, not structural

This document exists so that:

* design discussions stay precise
* expectations remain realistic
* the distinction between NNLM and conventional systems remains clear

If a word is overloaded — **stop and define it**.

-> See also: [Design Principles](DESIGN_PRINCIPLES.md) | [Glossary](../APPENDIX/GLOSSARY.md) | [Pipeline Model](../ARCHITECTURE/PIPELINE_MODEL.md)
