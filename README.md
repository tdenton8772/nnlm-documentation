# NNLM — NAM-Native Language Model

**NNLM** is a grounded question-answering language system built on [Neural Addressable Memory (NAM)](https://github.com/tdenton8772/nam-documentation) as its memory substrate.

It answers a question by planning how to search NAM, retrieving candidate records through NAM's address, vector and graph paths, deciding which of those records are evidence, and generating a short answer from only those records, with numbered citations. When the evidence is not there, it says so.

This repository is the **public documentation** for NNLM: what it is, how it is built, what it can and cannot do, and where it is going. It describes the system as it runs today. It deliberately leaves out private implementation detail.

---

## What Problem NNLM Solves

General-purpose language models answer from what they absorbed in training. They are fluent whether or not they are right, and they cannot show where an answer came from.

NNLM is built for a narrower and stricter job:

* every claim should trace to a specific stored record
* the system should decline when the stored records do not contain the answer
* retrieval, selection and generation should be separate, inspectable steps
* the answering model should be small enough to run on modest hardware

---

## What NNLM Is

* **NAM-native.** All evidence comes from NAM. NNLM keeps no knowledge store of its own; conversation turns are written back into NAM too.
* **Multi-stage.** Planning, retrieval, record selection and generation are separate stages with separate models and separate outputs you can inspect.
* **Selection before generation.** A trained selector (a TabTransformer) scores every retrieved record. Only the records it keeps are shown to the generator.
* **Encoder-decoder generation.** A fine-tuned T5 model writes the answer from the selected records. It is conditioned on those records, not on an open-ended prompt.
* **Citation-checked.** Answers cite records as `[N]`. A marker that does not point at a record the generator was shown is not counted as a citation and links to no record.
* **Provenance-visible.** Every record carries the NAM paths that reached it (address, vector, graph), and the UI shows them.

---

## What NNLM Is Not

* Not a wrapper around a hosted chat model.
* Not a "retrieve top-k and paste into a prompt" pipeline. Retrieved records are scored and filtered by a trained model first.
* Not a model that has memorised its knowledge. Knowledge lives in NAM.
* Not a general conversational assistant, creative writer or agent.

---

## How NNLM Relates to NAM

NAM provides the memory: storage, addressing, and a query service that fuses three retrieval paths (semantic addresses, vector similarity and a graph index) into one result set, tagging each row with the paths that produced it.

NNLM provides the language interface: it turns a question into a retrieval plan, calls NAM, selects evidence from what NAM returns, and writes a cited answer. It also stores each conversation turn back in NAM, so a session's history is itself retrievable memory.

NNLM cannot run without NAM.

---

## How to Read This Documentation

* **Non-technical readers:** `OVERVIEW/`, `PHILOSOPHY/`, `CAPABILITIES/`
* **Technical readers:** `ARCHITECTURE/PIPELINE_MODEL.md`, then `ARCHITECTURE/MULTI_STAGE_DESIGN.md` and `ARCHITECTURE/GROUNDING_MODEL.md`
* **ML engineers:** `ARCHITECTURE/RECORD_SELECTION.md`, `ARCHITECTURE/GENERATION_MODEL.md`, `ARCHITECTURE/SESSION_AND_CONTEXT.md`
* **Collaborators:** `GOVERNANCE/`, `ROADMAP/`

---

## Design Principles (Short Form)

* Grounding over fluency
* Citation over assertion
* Declining over guessing
* Structure over prompting
* Inspectable stages over a single opaque model
* Measurement over assumption

Expanded in `PHILOSOPHY/DESIGN_PRINCIPLES.md`.

---

## Current State

NNLM runs end to end against a live NAM cluster on Kubernetes:

* a web UI that shows each stage as it completes (plan, retrieve, select, generate), with per-record selection scores, citations and provenance badges
* an encoder service that plans retrieval with trained planner models, resolves follow-up pronouns from the session's stored turns, and queries NAM
* NAM's own encoder, run read-only alongside NNLM, which extracts entities and semantic signals with NAM's rule-based parser and encoder heads
* a decoder service that runs record selection and grounded generation, on CPU by default and on a GPU after an explicit prewarm
* session turns stored in NAM and reloaded by session id
* a one-shot `/ask` endpoint for programmatic callers

Answer quality depends first on retrieval: when NAM does not return a record that contains the answer, NNLM declines rather than guess. See [Current State](ROADMAP/CURRENT_STATE.md) and [Current Limitations](CAPABILITIES/CURRENT_LIMITATIONS.md).

---

## Licensing

NNLM is **source-available**, not open source. See `GOVERNANCE/LICENSING_MODEL.md`.

---

## Next Steps

1. `OVERVIEW/WHAT_IS_NNLM.md`
2. `ARCHITECTURE/PIPELINE_MODEL.md`
3. `CAPABILITIES/CURRENT_LIMITATIONS.md`
