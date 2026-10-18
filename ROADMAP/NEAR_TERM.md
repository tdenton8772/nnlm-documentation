# Near-Term Roadmap

**Scope:** making the existing pipeline answer more questions correctly, without changing its shape.

Nothing here is a commitment or a timeline. Items are ordered by expected impact on answer quality.

---

## 1. Retrieval Recall for Natural-Language Questions

**Goal:** NAM returns a record containing the answer for more ordinary questions.

Every answer starts with retrieval. Work here happens largely in NAM (its address, vector and graph paths and how they are fused) and in how NNLM phrases its queries and seeds its graph walks.

**Outcome:** fewer declines caused by a missing record, which no downstream model can fix.

---

## 2. A Better Selector and Generator Pair

**Goal:** the selector keeps the record that contains the answer, and the generator gives better-grounded answers and better-calibrated declines.

Candidate models are evaluated against the deployed pair on the graded question set, and replace it only if they beat it, including zero answers from parametric memory.

---

## 3. Close Train/Serve Differences

**Goal:** the selector sees at serving time the same features it was trained on.

Populate the selector inputs that are fixed at serving time.

---

## 4. Session Resolution

**Goal:** fewer wrong referents in follow-up questions.

Handle more than one pronoun per question, references to places and objects, and long sessions beyond the turns that are read back.

---

## 5. Operational Observability

**Goal:** the per-stage signals already in API responses (timings, guards, invalid citations, execution location, provenance) reach standard monitoring and alerting.

---

## Not in This Phase

* new pipeline stages
* learning during use
* multilingual support
* streaming generation
* autonomous or tool-using behaviour

-> See also: [Current State](CURRENT_STATE.md) | [Current Limitations](../CAPABILITIES/CURRENT_LIMITATIONS.md) | [Long-Term Direction](LONG_TERM.md)
