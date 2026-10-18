# What NNLM Can Do

This document lists what NNLM does in the running system.

---

## 1. Cited Question Answering

NNLM answers a question with a short response that cites the records it used as `[N]`. Each citation maps to a record in the UI's selection panel, and a marker that names no record the generator was shown is not counted as a citation.

---

## 2. Decline When the Evidence Is Not There

The system declines rather than guesses when:

* the retrieval quality check judges the results insufficient,
* no records come back,
* the selector keeps nothing, or
* the kept records do not contain the answer (the generator can decline in that case).

The first three are pipeline gates; the generator is not run at all.

---

## 3. Multi-Path Retrieval With Provenance

Each question is sent to NAM's query service, which fuses its semantic-address, vector-similarity and graph paths. NNLM can add its own graph walks from the question's entities. Every record carries the paths that reached it, shown in the UI as **NAM**, **VEC** and **GRAPH** badges, and the quality summary counts how many records a graph path supplied.

---

## 4. Trained Evidence Selection

A TabTransformer selector scores each retrieved record from its text and NAM's metadata, and only kept records reach the generator. Per-record scores and keep decisions are visible.

---

## 5. Multi-Turn Conversation

Turns are stored in NAM under a session id. Follow-up pronouns are resolved against stored turns before retrieval ("Where was he born?" after a question about a person); after a "Who killed X?" question, a follow-up pronoun refers to a name from the answer. Sessions survive reloads and can be reopened from the sidebar.

---

## 6. Live, Inspectable Pipeline

The UI shows each stage as it completes:

* **Plan** — resolved question, entities, scope, access patterns, graph hops; exportable as JSON or YAML
* **Retrieve** — the returned records with provenance badges and match scores, and counts of query, graph and session results
* **Select** — selector score per record, kept or not, guard fired if any
* **Generate** — the answer, with cited records highlighted

Per-stage timings (NAM encoder, planner, NAM query, quality check, selector, generator, end to end) are shown for every answer. A stage that was skipped by a gate is shown as skipped.

---

## 7. Runs Without a GPU

Every model runs on CPU by default. An operator can prewarm a GPU from the UI to speed up the decoder's sentence encoder and generator. A prewarmed GPU is held while in use and released on request or after a period of inactivity. Whenever no GPU is serving, and on any GPU failure, calls run on CPU. The UI shows the GPU's state, and one-shot responses report where the decode ran.

---

## 8. Programmatic Access

The encoder's `/ask` endpoint runs the full pipeline in one call and returns the answer, citations, cited record ids, invalid citations, selection detail, retrieval context, where the decode ran, and errors. Access can be protected with a shared-secret header.

---

## Summary

NNLM retrieves from NAM through several paths, selects evidence with a trained model, writes a short cited answer with a small generator, declines when it should, carries a conversation across turns, and shows every step.

-> See also: [What NNLM Cannot Do](WHAT_NNLM_CANNOT_DO.md) | [Current Limitations](CURRENT_LIMITATIONS.md) | [Grounding Model](../ARCHITECTURE/GROUNDING_MODEL.md)
