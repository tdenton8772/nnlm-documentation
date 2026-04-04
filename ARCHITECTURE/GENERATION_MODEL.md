# Generation Model

The generation stage is where structure becomes language.

It takes the output of record selection — a scored, filtered set of evidence — and produces a natural language response with citations.

This is the most visible stage of NNLM. It is also the most constrained.

---

## How Generation Is Conditioned

The generation model does not receive a text prompt with documents pasted in.

Instead, it receives:

* **Selected records** — the evidence that passed record selection
* **Learned representations** — structured signals from the selection stage that encode relevance information
* **The original query** — what the user asked

These inputs condition the model's behavior. The model generates within the boundary defined by this evidence — not from its general training knowledge.

---

## Soft Prompt Injection

The learned representations from record selection are injected into the generation model as **soft prompts**.

Soft prompts are not text. They are learned embeddings — continuous representations that carry structured information about:

* which records are relevant
* how strongly each record supports an answer
* the overall confidence of the selection stage

These representations are passed to the generation model alongside or in place of traditional text tokens.

### Why Soft Prompts Instead of Text

Text-based context has fundamental limitations:

* the model must parse and interpret text at generation time
* position in the context window affects attention
* more text does not reliably mean more information
* the model decides which text to attend to — often incorrectly

Soft prompts bypass these problems:

* they encode relevance signals directly, without requiring parsing
* they carry more information per token than text
* they are trained to convey exactly the signals the generation model needs
* the model does not need to "figure out" what matters — it is told structurally

This is the difference between giving someone a pile of documents and giving them a briefing.

---

## Citation Markers as Structural Output

Citations in NNLM are not added after generation.

The generation model is trained to produce citation markers as part of its output structure. When it makes a factual claim, it simultaneously produces a marker indicating which selected record supports that claim.

This means:

* **citations are verifiable** — each marker points to a specific record in the selection output
* **missing citations are detectable** — a factual claim without a citation marker is a measurable failure
* **false citations are identifiable** — a citation pointing to a record that does not support the claim can be caught
* **citation quality is a training signal** — the model learns to cite correctly, not just to generate fluently

Citation is not formatting. It is a **structural output constraint**.

---

## Why This Is Better Than Context Stuffing

Context stuffing — the RAG approach of inserting retrieved text into the prompt — has well-documented problems:

* **Lost in the middle** — models attend more to the beginning and end of long contexts
* **Context window limits** — there is a hard ceiling on how much text can be provided
* **Relevance confusion** — the model must determine which parts of the context are relevant
* **Blending** — the model may blend context information with training data seamlessly

NNLM's approach avoids these issues:

* the generation model receives a curated, filtered evidence set
* learned representations convey relevance signals directly
* the model is constrained to selected records, not free to blend
* citation markers create accountability for every claim

The result is generation that is **grounded by construction**, not by convention.

---

## Controlled Variability

The retrieval and selection stages of NNLM are deterministic.
The generation stage has controlled variability.

This means:

* given the same selected records, the generation model may produce slightly different phrasings
* the factual content remains anchored to the same evidence
* the citations remain the same
* only the surface language varies

This is intentional. Natural language has many valid ways to express the same grounded claim. Forcing exact reproduction of phrasing would reduce readability without improving accuracy.

The variability is in expression, not in substance.

---

## Rejection in Generation

When the selection stage passes too few records — or no records — the generation model produces a rejection response.

This is not a fallback phrase. It is a **structurally determined output**.

The generation model is trained to recognize when evidence is insufficient and to produce responses that:

* acknowledge the query
* indicate that sufficient evidence was not found
* avoid asserting anything ungrounded
* suggest potential reasons (when possible) without fabricating them

Rejection is a first-class output of the generation model, not an edge case handler.

---

## What Generation Does Not Do

The generation model does not:

* **retrieve information** — retrieval is already complete
* **determine relevance** — selection has already filtered
* **learn at query time** — the model is trained offline
* **access its training knowledge freely** — it is constrained to the evidence provided
* **produce ungrounded claims** — every assertion must be cited

These constraints are what make the generation stage trustworthy.
A less constrained model would be more fluent. It would also be less reliable.

---

## Summary

The NNLM generation model:

* receives conditioned input from the selection stage, not raw text
* uses soft prompt injection to incorporate learned relevance signals
* produces citations as structural output tokens, not post-processing
* operates within a boundary defined by selected evidence
* has controlled variability in expression, not in substance
* produces rejection as a first-class output when evidence is insufficient

Generation is the visible surface of NNLM.
Everything beneath it — encoding, retrieval, selection — exists to ensure that what it says can be trusted.

-> See also: [Record Selection](RECORD_SELECTION.md) | [Grounding Model](GROUNDING_MODEL.md) | [Pipeline Model](PIPELINE_MODEL.md) | [Design Principles](../PHILOSOPHY/DESIGN_PRINCIPLES.md)
