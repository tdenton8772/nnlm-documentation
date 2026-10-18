# References and Lineage

Ideas, systems and prior work that NNLM builds on. These are influences and components, not endorsements or claims of equivalence.

---

## Foundation: Neural Addressable Memory (NAM)

NNLM's memory substrate: storage, semantic addressing, vector search, a graph index, and a query service that fuses them with per-record provenance.

-> NAM Documentation: [https://github.com/tdenton8772/nam-documentation](https://github.com/tdenton8772/nam-documentation)

## NNLM Source

Source-available under a separate license. The source repository is not public; see [Licensing Model](../GOVERNANCE/LICENSING_MODEL.md).

---

## Models Used

### T5

* Raffel et al. (2020). "Exploring the Limits of Transfer Learning with a Unified Text-to-Text Framework."

NNLM's generator is a fine-tuned T5 encoder-decoder, and its two planner models (retrieval planning and sufficiency checking) are T5-based text-to-text models.

### TabTransformer

* Huang et al. (2020). "TabTransformer: Tabular Data Modeling Using Contextual Embeddings."

NNLM's record selector builds on the TabTransformer: NAM's categorical metadata is embedded and combined with sentence embeddings of the question and record, and attention runs across the records of one question before each is scored.

### Sentence embeddings

* Reimers and Gurevych (2019). "Sentence-BERT: Sentence Embeddings using Siamese BERT-Networks."

The selector's text inputs come from a pretrained sentence-embedding model.

---

## Related Ideas

### Retrieval-augmented generation

* Lewis et al. (2020). "Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks."

The pattern NNLM extends: retrieve, then generate. NNLM inserts trained selection, generation gates and citation checking between the two.

### Pointer-generator networks

* See et al. (2017). "Get To The Point: Summarization with Pointer-Generator Networks."

An influence on citation as pointing: NNLM's `[N]` markers point at whole records rather than source tokens.

---

## Systems Thinking

* **Separation of concerns.** Single-purpose stages with explicit JSON contracts.
* **Fail visibly.** Gates, guards, invalid-citation counts and execution location are surfaced rather than hidden.
* **Degrade, don't stop.** GPU acceleration is optional and every accelerated call has a CPU fallback.
* **Designed backward from failures.** Several mechanisms (the empty gate, past-answer exclusion, the rank floor in the keep rule) exist because a specific failure was observed and measured.

---

## Deliberately Not Used

* large hosted language models in the primary answer path
* reinforcement learning from human feedback
* learning at query time
* autonomous agent frameworks

-> See also: [Design Principles](../PHILOSOPHY/DESIGN_PRINCIPLES.md) | [Where NNLM Fits](../OVERVIEW/WHERE_NNLM_FITS.md) | [Why Not RAG](../PHILOSOPHY/WHY_NOT_RAG.md)
