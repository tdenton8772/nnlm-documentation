# Long-Term Direction

Where NNLM is intended to go. This is directional intent, not a commitment, and every item is constrained by the same rules: answer from evidence, cite it, decline otherwise, keep every stage inspectable.

---

## 1. Domain Adaptation as a Workflow

Knowledge comes from NAM, so new data needs no retraining. The long-term aim is that an organisation can also point the same pipeline at its own data in NAM and adapt the selector and generator to it offline, without architectural change.

---

## 2. Better Use of NAM's Structure

NAM exposes more than text: semantic address axes, match geometry, graph relations, and per-path provenance. The selector uses part of this, and the generator sees only text. Directions include:

* richer selector inputs from NAM's address and graph signals
* multi-hop answers assembled from records reached along graph paths
* using provenance (which paths agreed on a record) as evidence strength

---

## 3. More Informative Declines

Declines say the records do not contain the answer. Longer term, a decline could say *why*: nothing retrieved, retrieved but off-subject, or partially answered, and suggest a better-formed question. It must become more useful without becoming less honest.

---

## 4. Streaming Generation

Streaming tokens would reduce perceived latency, but citations are checked after generation and a streamed claim cannot be withdrawn. Any streaming design must keep the citation check meaningful.

---

## 5. Multilingual Support

Planning, selection and generation would each need training data in the new language, and declines and citations would need to be validated per language. Translation alone is not enough.

---

## 6. Multiple NAM Instances

Querying several NAM clusters (for example, per domain) behind one NNLM, with selection operating over evidence from more than one source.

---

## 7. A Stable External API

`/ask` serves programmatic callers. A long-term API would version that contract and expose per-stage results (plan, retrieval provenance, selection detail), not just the final answer, because inspectability is the point.

---

## The Constant

The models will change. The structure — separate stages, a selector before the generator, gates that stop generation without evidence, checked citations — is meant to outlast any particular model.

-> See also: [Current State](CURRENT_STATE.md) | [Near-Term Roadmap](NEAR_TERM.md) | [Design Principles](../PHILOSOPHY/DESIGN_PRINCIPLES.md)
