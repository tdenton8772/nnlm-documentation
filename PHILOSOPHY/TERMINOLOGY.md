# Terminology

*How key terms are used within NNLM.* Where a term here overlaps with industry usage, this definition applies in these documents.

---

## Grounding

The connection between a claim in an answer and a record the generator was shown. Enforced by structure: evidence filtering, selection, generation gates, and citation checking. Grounding means **traceable**, not **guaranteed correct**.

---

## Record

A document returned by NAM, normalised into the shape the decoder consumes: text, record id, NAM address metadata (ontology, entity, match score, match geometry), vector similarity when available, and `retrieved_via`.

---

## Provenance (`retrieved_via`)

The NAM access paths that reached a record, joined with `+`: for example `address`, `vector`, `graph`, `address+graph`. Shown in the UI as **NAM**, **VEC** and **GRAPH** badges. Records from NNLM's own graph walk are marked `graph`.

---

## Access Paths

NAM's retrieval paths inside one `/v1/query` call: **address** (matching on semantic address axes), **vector** (embedding similarity) and **graph** (traversal of NAM's graph index). NAM returns a per-path trace saying which ran, why, and what each contributed.

---

## Query Plan

The plan stage's output: scope, access patterns (`query`, `graph_walk`, `session_scan`, and `nam_graph` when NAM's graph path contributed), query texts, graph seeds and hops, and search geometry. Inspectable and exportable from the UI.

---

## Supervisor (Planner)

A trained T5 query-planner model that reads the question, NAM encoder features and history, and decides scope, graph use, hops and geometry.

---

## Quality Agent

A second trained T5 planner that judges whether retrieval results are sufficient, and if not, whether to add a graph walk or broaden the search geometry. Its verdict (`sufficient`) gates generation.

---

## Record Selection / Selector

The stage that decides which records the generator may see. The selector is a TabTransformer that scores each record from sentence embeddings and NAM metadata. A record is kept if it clears a fixed threshold or ranks among a fixed number of the highest-scoring records.

---

## Guard

A fixed rule applied after scoring: **source floor** (add back the best source record if only echoes of earlier answers were kept) and **empty gate** (no kept records means no generation). The guard that fired is shown in the UI.

---

## Noise Record

A retrieved record that does not help answer the question. Expected, because retrieval is broad; removed by selection.

---

## Poisoned Record

A record that must never be evidence regardless of score: a turn from another session, NNLM bookkeeping, or a stored question-answer pair (the system's own past output). Removed before selection.

---

## Render

The text input given to the generator: the question followed by the kept records, numbered `[1]`, `[2]`, ..., each truncated. The exact layout is part of each generator's contract and must match how it was trained.

---

## Citation

An `[N]` marker in an answer naming a rendered record. Checked after generation; markers that name nothing shown are left out of the citations and reported as **invalid citations**.

---

## Decline

An answer stating the records do not support a response. Either **structural** (a gate stopped generation) or **generated** (the generator, shown records without the answer, declined).

---

## Session / Turn

A conversation, identified by a session id. Each **turn** (question, answer, entities, top record excerpts) is stored in NAM under a key made from the session id and the turn number, and read back to resolve follow-up references.

---

## Referent

The entity a follow-up pronoun is resolved to: normally the previous question's subject, or a name from the answer after a "Who killed X?"-style question.

---

## Prewarm

An explicit request to provision a GPU for the decoder. Without a prewarm, nothing is provisioned and everything runs on CPU. A GPU session ends on release or after a period of inactivity.

---

## Execution

Where a one-shot decode was computed: `gpu` or `local`. Reported by `/decode` and `/ask`.

---

## Memory Substrate

The system that holds NNLM's knowledge: NAM. NNLM keeps no knowledge store of its own.

---

## Ontology, Affordance, Match Geometry, Match Score

NAM metadata on a record, used by the selector:

* **Ontology** — what kind of thing the record describes (person, location, organization, event, ...).
* **Affordance** — what kind of answer the query asks for (a person, a time, a location, ...), detected by NAM.
* **Match geometry** — how specifically the record matched the query in NAM's address space (point, line, plane, volume).
* **Match score** — how many semantic address axes matched. Kept separate from vector similarity, which is on a different scale.

---

## Why Terminology Matters

Say "kept records" when you mean what the generator saw, not "retrieved records". Say "traceable" when you mean grounded, not "correct". Say which access path found a record, not just that it was "retrieved".

-> See also: [Design Principles](DESIGN_PRINCIPLES.md) | [Glossary](../APPENDIX/GLOSSARY.md) | [Pipeline Model](../ARCHITECTURE/PIPELINE_MODEL.md)
