# Frequently Asked Questions (FAQ)

This FAQ answers the questions we hear most often from engineers, architects, and evaluators encountering NNLM for the first time.

If you are looking for marketing claims, this is not the right place.
If you are looking to understand what NNLM *actually does*, this is.

---

## Is this a chatbot?

No.

NNLM does not engage in general conversation, express opinions, role-play, or generate creative content.

It answers questions by:

* retrieving evidence from NAM
* filtering that evidence for relevance
* generating a cited response from the filtered evidence

If the evidence is insufficient, it declines to answer.

That is the full scope.

---

## Is this RAG?

No.

RAG retrieves documents by similarity, inserts them into a prompt, and hopes the model uses them correctly.

NNLM:

* retrieves by deterministic addressing via NAM
* filters noise records before generation
* conditions generation on learned representations, not just text
* requires citations as structural output
* supports rejection when evidence is insufficient

NNLM and RAG solve the same problem at different levels of architectural rigor.

-> See: [Why Not RAG](../PHILOSOPHY/WHY_NOT_RAG.md)

---

## Does it hallucinate?

By design, NNLM cannot assert ungrounded claims.

Every factual assertion must cite a retrieved record that was scored as relevant by the selection stage. If no records pass selection, the system rejects the query rather than generating.

This does not mean responses are always correct — if NAM contains incorrect records, NNLM will cite them faithfully. But every claim is traceable to its source, making errors identifiable and fixable.

---

## Does it need an LLM?

Yes — but the LLM is structurally constrained.

The generation stage uses a language model. However:

* it does not generate from training weights
* it is conditioned on pre-filtered evidence
* it must produce citations for every factual claim
* it cannot access knowledge outside the selected records

The language model provides fluency.
The pipeline provides grounding.

---

## Is it deterministic?

The retrieval and selection stages are fully deterministic. Given the same query and the same NAM state, the same records are retrieved and the same selection decisions are made.

The generation stage has controlled variability — it may produce slightly different phrasings, but the factual content and citations remain stable.

---

## Can it say "I don't know"?

Yes. And it does.

When record selection determines that no retrieved records sufficiently address the query, the system produces a rejection response.

This is a first-class output, not a fallback. Rejection is measurable, inspectable, and structurally determined — not a generated phrase.

---

## How does it differ from ChatGPT + search?

ChatGPT with search:

* uses web search to find information
* inserts results into the conversation context
* generates freely, with or without using the search results
* may blend search results with training data
* has no structural citation enforcement

NNLM:

* uses NAM for deterministic, address-based retrieval
* filters noise records before generation sees them
* constrains generation to selected evidence only
* requires citations as structural output
* rejects queries when evidence is insufficient

The difference is not search quality. It is architectural accountability.

---

## Can it work with custom data?

Yes.

NNLM operates on whatever data has been ingested into NAM. The system is designed to work with domain-specific knowledge bases.

Adapting NNLM to a new domain involves:

* ingesting domain data into NAM
* training the record selection model on domain-relevant examples
* training the generation model on domain-specific citation patterns

The pipeline architecture remains the same across domains.

---

## What if NAM is wrong?

If NAM contains incorrect records, NNLM will cite them faithfully.

NNLM does not verify the truth of records — it verifies that its claims are grounded in retrieved evidence.

The difference matters:

* **grounding** means every claim traces to a source
* **correctness** means the source is accurate

NNLM guarantees grounding. Correctness is a property of the data in NAM.

If NAM is wrong, NNLM will be wrong — but traceably wrong. The citation tells you exactly which record is the source, making the error identifiable and correctable.

---

## How fast is it?

Current end-to-end latency is approximately 5-6 seconds on CPU infrastructure.

The generation stage is the primary contributor. GPU inference is expected to reduce this to sub-1 second.

-> See: [Current Limitations](../CAPABILITIES/CURRENT_LIMITATIONS.md)

---

## Can it handle multi-turn conversations?

Yes.

NNLM supports session-based memory that tracks:

* entities discussed in previous turns
* pronoun and coreference resolution
* topic continuity

This is achieved through structured session state, not by stuffing previous turns into a context window.

-> See: [Session and Context](../ARCHITECTURE/SESSION_AND_CONTEXT.md)

---

## Is it production-ready?

NNLM is deployed and producing grounded responses from NAM.

It is suitable for:

* controlled evaluations
* domain-specific pilots
* environments where grounding and citation are requirements

It is not yet a fully managed platform. Latency, corpus size, and operational tooling are still maturing.

-> See: [Current State](../ROADMAP/CURRENT_STATE.md)

---

## Where should I start next?

If you want:

* conceptual clarity -> [Multi-Stage Design](../ARCHITECTURE/MULTI_STAGE_DESIGN.md)
* architectural detail -> [Pipeline Model](../ARCHITECTURE/PIPELINE_MODEL.md)
* practical constraints -> [What NNLM Can Do](../CAPABILITIES/WHAT_NNLM_CAN_DO.md) | [Current Limitations](../CAPABILITIES/CURRENT_LIMITATIONS.md)
* current status -> [Current State](../ROADMAP/CURRENT_STATE.md)
* future direction -> [Near-Term](../ROADMAP/NEAR_TERM.md) | [Long-Term](../ROADMAP/LONG_TERM.md)
* definitions -> [Terminology](../PHILOSOPHY/TERMINOLOGY.md) | [Glossary](GLOSSARY.md)

This documentation is designed to be read non-linearly.

-> See also: [Where NNLM Fits](../OVERVIEW/WHERE_NNLM_FITS.md) | [Terminology](../PHILOSOPHY/TERMINOLOGY.md) | [Current State](../ROADMAP/CURRENT_STATE.md)
