# Current Limitations

These are present limitations of the running system: maturity, coverage and engineering gaps. Permanent non-goals are in [What NNLM Cannot Do](WHAT_NNLM_CANNOT_DO.md).

---

## 1. Answers Depend on Retrieval

NNLM can only answer from records NAM returns. When the records that come back share words with the question but are about a different subject (a different person with the same surname, a different sense of a term), NNLM declines or answers only partially.

No downstream model can recover a record that was never retrieved, so improving retrieval for natural-language questions has the most leverage on answer quality.

---

## 2. Selection Can Drop the Answer

When the answer-bearing record is retrieved, the selector does not always keep it. The rank floor in the keep rule, on top of the score threshold, recovers most of these cases.

---

## 3. Generator Quality

The generator is a small model. It can:

* decline when a kept record does contain the answer,
* overstate or misattribute what a cited record says,
* phrase answers awkwardly.

A candidate model or configuration replaces the deployed one only if it beats it on a fixed, graded question set: answer correctness, attribution, unsupported entity mentions and decline correctness, with zero answers drawn from parametric memory.

---

## 4. Train/Serve Differences

A few selector inputs are populated in training but fixed at serving time. Until these match, part of the selector's design capacity is unused.

---

## 5. Latency on CPU

On CPU, a question takes many seconds end to end; the sentence encoding for selection and the generation are the largest costs. A prewarmed GPU takes over those two, but a GPU takes minutes to boot, so it must be prewarmed before use. The planners and NAM retrieval run on CPU either way.

---

## 6. Session Handling

* Pronoun resolution is rule-based and replaces one pronoun per question.
* It favours the previous question's named subject; references to places, objects or a second person can resolve wrongly.
* Only the first twenty turns of a session are read back.

---

## 7. English Only

NNLM is built and evaluated for English questions and records.

---

## 8. Operational Tooling

* Per-stage timings, provenance and GPU state are visible in the UI and in API responses.
* Metrics export to external monitoring and automated alerting are not in place.
* The decoder and GPU serving images must be released as a pair; the decoder refuses to prewarm an unpaired GPU image rather than silently serving different models.

---

## What Works Well

* end-to-end cited answers with checked citations
* structural declines when retrieval or selection yields nothing
* multi-path retrieval with visible provenance
* staged, inspectable UI with per-stage timing
* multi-turn conversation with stored, reloadable sessions
* CPU-only operation, with optional GPU acceleration

-> See also: [What NNLM Can Do](WHAT_NNLM_CAN_DO.md) | [What NNLM Cannot Do](WHAT_NNLM_CANNOT_DO.md) | [Current State](../ROADMAP/CURRENT_STATE.md)
