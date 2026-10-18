# Why NNLM Exists

NNLM exists because a language model that answers from its own weights cannot tell you where an answer came from, and will answer whether or not it knows.

---

## The Core Problem

A generative model produces likely text. Likely is not the same as true, and the model has no internal signal that separates recall from invention.

Retrieval helps, but in the common pattern the retrieved text is simply handed to a large model, which is still free to ignore it, blend it with what it already "knows", or cite it loosely.

---

## What NNLM Changes

NNLM takes three decisions away from the generator and makes them explicit stages:

1. **What to search for.** A planning stage works out the question's subject, resolves references to earlier turns, and chooses which NAM access patterns to use.
2. **What counts as evidence.** A trained selector scores every retrieved record. The generator never sees records the selector dropped.
3. **Whether to answer at all.** If retrieval is judged insufficient, or the selector keeps nothing, generation does not run and the system declines.

What is left for the generator is narrow: write a short answer from the numbered records in front of it, and cite them.

---

## Why an Encoder-Decoder, and Why Small

NNLM's generator is a fine-tuned T5 encoder-decoder model rather than a large decoder-only chat model. The reasons:

* **Conditioned on the records.** The encoder reads the question and the selected records; the decoder writes from that encoding. The model is trained to answer from what it is given, not from parametric memory.
* **Runs on modest hardware.** It runs on CPU from int8-quantized weights. A GPU makes it faster but is never required.
* **Citations are structural.** Records are numbered in the input and the model emits `[N]` markers that are checked against that numbering.
* **Says when evidence is insufficient.** It can decline when the records it is shown do not contain the answer, and it is not run at all when nothing was selected.

A small model will be less fluent than a large one. That trade is deliberate.

---

## Why NAM Is the Substrate

NAM supplies what NNLM needs from memory:

* multiple retrieval paths (semantic addresses, vector similarity, a graph index) fused into one result
* per-record provenance: which paths reached each record
* metadata the selector can use (ontology, match geometry, match score, similarity)
* a place to store conversation turns so they become retrievable memory

---

## The Design Bet

> Grounding is easier to enforce with structure — separate stages, a selector, checked citations — than by instructing a large model to behave.

The bet does not make NNLM always right. It makes it **inspectable**: when an answer is wrong, you can see whether retrieval missed, selection dropped the right record, or the generator misused what it had.

---

## In One Line

**NNLM exists to make "answer only from the evidence, and show it" a property of the pipeline rather than a request to a model.**

-> See also: [Where NNLM Fits](WHERE_NNLM_FITS.md) | [Design Principles](../PHILOSOPHY/DESIGN_PRINCIPLES.md) | [Generation Model](../ARCHITECTURE/GENERATION_MODEL.md)
