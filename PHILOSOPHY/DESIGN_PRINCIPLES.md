# Design Principles

NNLM is not defined by which models it uses.
It is defined by the **constraints it refuses to violate**.

These principles are not aspirational.
They are *design laws* that shape every architectural decision, even when doing so is inconvenient.

---

## 1. Grounding Over Fluency

NNLM will never sacrifice factual grounding for more natural-sounding output.

A response that is awkwardly phrased but correctly cited is always preferred over a response that reads well but cannot be verified.

### Why this matters

* Fluency creates false confidence
* Ungrounded fluency is the definition of hallucination
* Users trust well-written text — even when it is wrong
* Grounding is the only defense against plausible nonsense

If a claim cannot be tied to a retrieved record, it does not appear in the output.

---

## 2. Citation Over Assertion

Every factual claim in an NNLM response must cite its source.

Citation is not a formatting feature. It is a **structural requirement** of the generation stage.

### Why this matters

* Assertions without evidence are indistinguishable from hallucination
* Citations enable verification
* Citations enable trust
* Citations enable debugging when something is wrong

If the system cannot cite, it does not assert.

---

## 3. Rejection Over Hallucination

NNLM is designed to say "I don't know" when evidence is insufficient.

This is not a fallback. It is a **first-class response**.

### Why this matters

* Silence is more honest than invention
* Users can act on "I don't know" — they cannot act safely on fabricated answers
* Rejection signals a gap in the knowledge base, which is actionable
* Hallucination signals nothing — it looks the same as a correct answer

The system is calibrated to prefer rejection over low-confidence generation.

---

## 4. Structure Over Prompting

NNLM achieves its guarantees through pipeline architecture, not through prompt engineering.

The system does not ask the language model to behave well.
It **structures the pipeline** so that misbehavior is not possible.

### Why this matters

* Prompts are suggestions — architecture is enforcement
* Prompt-based controls are brittle across model versions
* Structural guarantees survive model upgrades
* Debugging prompt failures is guesswork; debugging pipeline failures is engineering

Every guarantee in NNLM is a consequence of design, not instruction.

---

## 5. Determinism Over Creativity

The retrieval and selection stages of NNLM are deterministic.

Given the same query and the same NAM state, the same records are retrieved and the same selection decisions are made.

### Why this matters

* Determinism makes systems reproducible
* Determinism makes systems debuggable
* Determinism makes systems auditable
* Determinism makes failures actionable

The generation stage has controlled variability — but it operates only on deterministically selected evidence.

Creativity is not a goal. Correctness is.

---

## 6. Measurement Over Assumption

Every stage in the NNLM pipeline produces measurable outputs.

Encoding produces a query plan. Retrieval produces records with metadata. Selection produces scored records with keep/reject decisions. Generation produces cited text.

### Why this matters

* If you cannot measure a stage, you cannot improve it
* If you cannot observe a failure, you cannot fix it
* Per-stage measurement enables targeted improvement
* End-to-end measurement enables latency optimization

NNLM is designed to be inspected, not trusted blindly.

---

## 7. Multi-Stage Over Monolith

NNLM is built as a pipeline of independent stages, not a single model.

Each stage has its own model, its own training, its own evaluation, and its own failure modes.

### Why this matters

* Monolithic models hide their reasoning
* Multi-stage pipelines expose their reasoning
* Individual stages can be improved, replaced, or debugged independently
* Failure in one stage does not silently corrupt the entire output

A pipeline is harder to build than a monolith.
It is easier to trust.

---

## 8. NAM as Substrate, Not Accessory

NAM is not a pluggable component in NNLM. It is the **foundation**.

NNLM's guarantees — deterministic retrieval, rich metadata, structured addressing, auditability — all derive from NAM.

### Why this matters

* Without NAM, NNLM would be another RAG system
* NAM provides the determinism that makes grounding structural
* NAM provides the metadata that makes selection possible
* NAM provides the addressing that makes citation meaningful

NNLM and NAM are co-dependent by design.
Neither should be deployed without the other.

---

## Closing Thought

NNLM is not built to impress demos.

It is built to survive:

* production scrutiny
* regulatory audits
* user skepticism
* changing requirements

These principles exist to ensure that the system produces **trustworthy outputs long after its creators are no longer in the room**.

-> See also: [Why NNLM Exists](../OVERVIEW/WHY_NNLM_EXISTS.md) | [Terminology](TERMINOLOGY.md) | [Multi-Stage Design](../ARCHITECTURE/MULTI_STAGE_DESIGN.md)
