# NNLM — NAM-Native Language Model

**NNLM** is a language model built entirely on [Neural Addressable Memory (NAM)](https://github.com/tdenton8772/nam-documentation) as its memory substrate.

NNLM is not a chatbot, not a RAG system, and not a fine-tuned LLM with retrieval bolted on.
It is a different approach to language — one that treats **memory as deterministic structure**, not probabilistic context.

This repository contains the **public documentation** for NNLM: its philosophy, architecture, capabilities, and roadmap. It intentionally excludes private implementation details.

---

## What Problem NNLM Solves

Modern language models have a fundamental problem: they hallucinate.

They generate fluent, confident text that has no grounding in reality. Retrieval-augmented generation (RAG) was supposed to fix this, but RAG systems:

* stuff context windows with ranked similarity results
* have no mechanism to reject irrelevant records
* cannot explain which records contributed to which claims
* lose coherence as context grows
* treat retrieval and generation as loosely coupled stages

NNLM addresses a different class of problems:

* "Every claim must trace to a specific source"
* "The system should say 'I don't know' when it doesn't know"
* "Retrieval and generation should be structurally coupled, not just pipelined"
* "The system must be deterministic and inspectable at every stage"

---

## What NNLM Is

NNLM is a **grounded language model** with the following defining characteristics:

* **NAM-native**
  Memory is not retrieved by similarity. It is navigated via deterministic address-based access through NAM.

* **Structurally grounded**
  Generation is constrained by what memory actually contains. Claims cite specific records. Noise is filtered before generation begins.

* **Multi-stage**
  Encoding, record selection, and generation are distinct stages with independent models, each inspectable and measurable.

* **Citation-first**
  Every substantive claim in a response traces to a specific memory record via `[N]` citation markers. This is architectural, not cosmetic.

* **Rejection-aware**
  The system distinguishes between records that support an answer and records that are noise. Noise records are filtered before generation, not after.

---

## What NNLM Is Not

To avoid confusion, NNLM is explicitly **not**:

* ❌ A chatbot wrapper around an LLM
* ❌ A RAG system with a vector database
* ❌ A fine-tuned LLM that memorizes training data
* ❌ A prompt engineering framework
* ❌ A search engine with generative summaries
* ❌ A system that trades accuracy for fluency

NNLM does **not** optimize for "most helpful response."
It optimizes for **grounded, traceable, deterministic answers**.

---

## How NNLM Relates to NAM

NAM provides the **memory substrate** — deterministic, geometry-based retrieval of structured information.

NNLM provides the **language interface** — translating natural language queries into NAM address projections, selecting relevant records from results, and generating grounded responses.

The relationship is structural:

* NAM handles **what to retrieve** (via address navigation)
* NNLM handles **what to say** (via grounded generation)
* Neither component works alone — they are co-dependent by design

NNLM does not replace NAM. It does not duplicate NAM's retrieval. It consumes NAM's output and produces language.

---

## How to Read This Documentation

This documentation is organized for different audiences:

* **Non-technical / GTM / Advisors**
  Start with:

  * `OVERVIEW/`
  * `PHILOSOPHY/`
  * `CAPABILITIES/`

* **Technical but non-implementers**
  Focus on:

  * `ARCHITECTURE/`
  * `ARCHITECTURE/PIPELINE_MODEL.md`
  * `ARCHITECTURE/GROUNDING_MODEL.md`
  * `ARCHITECTURE/MULTI_STAGE_DESIGN.md`

* **AI/ML researchers and engineers**
  Focus on:

  * `ARCHITECTURE/RECORD_SELECTION.md`
  * `ARCHITECTURE/GENERATION_MODEL.md`
  * `PHILOSOPHY/DESIGN_PRINCIPLES.md`

* **Potential collaborators**
  Review:

  * `GOVERNANCE/`
  * `ROADMAP/`

This repo is designed to be readable **without access to the private codebase**.

---

## Design Philosophy (High Level)

NNLM is built around a small set of non-negotiable principles:

* **Grounding over fluency**
* **Citation over assertion**
* **Rejection over hallucination**
* **Structure over prompting**
* **Determinism over creativity**
* **Measurement over assumption**

These principles are expanded in `PHILOSOPHY/DESIGN_PRINCIPLES.md`.

---

## Current State of the Project

NNLM is in **active development** with a working end-to-end pipeline deployed against a live NAM cluster.

* The encoder pipeline — multi-agent architecture with supervisor, entity resolution, retrieval, and quality assessment — is implemented and operational
* Record selection uses a trained model that scores records as signal vs noise using NAM's native metadata
* Generation produces citation-grounded responses constrained to selected records
* Multiple fallback paths ensure the system always responds, with the primary path using trained models and fallbacks using external LLMs
* Per-stage latency is tracked and visible in the UI for every query
* The system supports multi-turn conversation with session-based pronoun resolution

This is **not** a finished product, and this repo is intentionally transparent about current limitations.

> See: [Current State](ROADMAP/CURRENT_STATE.md) | [Current Limitations](CAPABILITIES/CURRENT_LIMITATIONS.md)

---

## Licensing & Access

NNLM follows the same licensing model as NAM: **source-available**, not open source.

See `GOVERNANCE/LICENSING_MODEL.md` for specifics.

---

## Why This Repo Exists

This repository exists to:

* Establish a shared mental model for NNLM
* Explain how a language model can be built on deterministic memory
* Document the architectural decisions that make grounded generation possible
* Prevent mischaracterization as "just another RAG chatbot"
* Serve as the authoritative public reference

If you are evaluating, advising, or helping bring NNLM to market, **this repo is the source of truth**.

---

## Next Steps

If you're new:

1. Read `OVERVIEW/WHAT_IS_NNLM.md`
2. Read `PHILOSOPHY/DESIGN_PRINCIPLES.md`
3. Skim `ARCHITECTURE/MULTI_STAGE_DESIGN.md`

If you're considering engagement, reach out through the appropriate private channel.

---

**NNLM is about building language models that can prove what they know.**
