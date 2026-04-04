# References and Lineage

This document lists **ideas, systems, and prior art** that influenced the design of NNLM.

These references are **conceptual inspirations**, not dependencies, endorsements, or claims of equivalence.
NNLM intentionally diverges from many of these approaches.

---

## Foundation: Neural Addressable Memory (NAM)

NNLM is built on NAM as its memory substrate.

NAM provides:

* deterministic, address-based semantic retrieval
* rich metadata (ontology, geometry, match scores)
* structured address space for knowledge organization
* auditability and reproducibility

Without NAM, NNLM would not exist.

-> NAM Documentation: [https://github.com/neural-addressable-memory/nam-documentation](https://github.com/neural-addressable-memory/nam-documentation)

---

## NNLM Source

NNLM source code is available under a source-available license.

-> NNLM Repository: [https://github.com/neural-addressable-memory/nnlm](https://github.com/neural-addressable-memory/nnlm) (private)

---

## Retrieval-Augmented Generation

### RAG

* Lewis et al. (2020). "Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks."

RAG established the pattern of combining retrieval with generation.
NNLM acknowledges this contribution but addresses RAG's structural limitations — specifically the absence of noise filtering, the lack of structural citation enforcement, and the reliance on embedding similarity for retrieval.

NNLM is not an improvement on RAG. It is a different architecture that solves the same problem.

---

## Relevant Concepts

### TabTransformer

* Huang et al. (2020). "TabTransformer: Tabular Data Modeling Using Contextual Embeddings."

The concept of applying attention-based architectures to tabular and structured data influenced NNLM's approach to record selection.

When record selection evaluates retrieved records, it operates on structured metadata — not free text. The insight that structured features benefit from contextual embedding informed the selection architecture.

---

### Sequence-to-Sequence Models

* Raffel et al. (2020). "Exploring the Limits of Transfer Learning with a Unified Text-to-Text Framework."

The text-to-text paradigm influenced NNLM's generation stage.

The concept of framing diverse language tasks as text-to-text problems is relevant to NNLM's approach of generating cited responses from structured evidence.

---

### Soft Prompts and Prefix Tuning

* Lester et al. (2021). "The Power of Scale for Parameter-Efficient Prompt Tuning."
* Li and Liang (2021). "Prefix-Tuning: Optimizing Continuous Prompts for Generation."

The concept of conditioning language models via learned continuous representations — rather than discrete text prompts — influenced NNLM's approach to passing selection signals to the generation model.

Soft prompt injection in NNLM carries structured relevance information from the selection stage, providing richer conditioning than text-based context stuffing.

---

### Pointer-Generator Networks

* See et al. (2017). "Get To The Point: Summarization with Pointer-Generator Networks."

The concept of models that can both generate from vocabulary and point to specific source tokens influenced NNLM's citation mechanism.

NNLM's generation model produces citation markers that point to specific retrieved records — a structural echo of the pointer mechanism, applied at the record level rather than the token level.

---

## Systems Thinking

### Deterministic Systems

* Event-sourced architectures influenced NNLM's insistence on reproducibility
* The principle that derived state should be rebuildable from inputs

### Separation of Concerns

* The Unix philosophy of composable, single-purpose tools influenced NNLM's multi-stage pipeline design
* Each stage does one thing and has clear contracts with its neighbors

### Failure Mode Design

* NNLM was designed backward from failure cases
* The architecture is shaped by asking "what goes wrong?" not "what would be cool?"

---

## Explicit Non-Influences

For clarity, NNLM intentionally does **not** draw from:

* autonomous agent frameworks
* reinforcement learning from human feedback (RLHF)
* chain-of-thought prompting as an architecture (though the encoder may use it internally)
* embedding-based similarity search as a retrieval primitive

These omissions are deliberate.

---

## Closing Note

NNLM is not an incremental improvement on RAG.
It is an attempt to establish **structurally grounded, cited language generation** as a distinct architectural category.

The references above represent starting points — not destinations.

-> See also: [Design Principles](../PHILOSOPHY/DESIGN_PRINCIPLES.md) | [Where NNLM Fits](../OVERVIEW/WHERE_NNLM_FITS.md) | [Why Not RAG](../PHILOSOPHY/WHY_NOT_RAG.md)
