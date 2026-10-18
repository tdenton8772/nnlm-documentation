# Grounding Model

Grounding in NNLM means: **every claim in an answer should come from a record the generator was shown, and the answer says which.**

This document lists the mechanisms that push the system toward that, and is explicit about what they do and do not guarantee.

---

## The Mechanisms

### 1. All evidence comes from NAM

NNLM has no knowledge store of its own. Every record the generator sees was returned by NAM's query service or reached by a NAM graph walk in the same request. Each record carries `retrieved_via`, the NAM paths that reached it (address, vector, graph, or a combination).

### 2. Bad evidence is filtered before selection

Some things NAM can return are never valid evidence and are removed unconditionally:

* turns stored by *other* conversation sessions;
* NNLM's own bookkeeping documents (for example the session index);
* stored question-answer pairs, including from the current session. A previous answer is the system's own output, and treating it as evidence would let one bad answer reinforce itself.

### 3. A trained selector chooses the evidence

The TabTransformer scores every remaining record, and only kept records go forward. See [Record Selection](RECORD_SELECTION.md).

### 4. Generation is gated

The generator does not run if retrieval was judged insufficient, if there are no records, or if selection kept nothing. A model fine-tuned to answer will otherwise answer from its weights when given nothing; the gate removes that possibility.

### 5. The generator only sees kept records

Records selection dropped are not rendered into the generator's input. The model cannot quote or cite what it was not given.

### 6. Citations are checked

Each `[N]` in the answer must point at a record that was shown. A marker that does not is left out of the citation list and links to no record; the decoder logs it, and the one-shot API reports it as `invalid_citations`.

---

## Declining

NNLM declines in two ways:

* **Structurally**, when a gate fires (insufficient quality, no records, nothing selected). The reply comes from the pipeline, not the model.
* **From the generator**, when it was shown records that do not contain the answer and says so.

Declining is treated as a correct outcome when the records do not support an answer.

---

## What Grounding Does Not Guarantee

* **Correctness of the source.** If a NAM record is wrong, a grounded answer will repeat it, with a citation that lets you find it.
* **That every sentence is supported.** The generator is a trained model. It is conditioned on the records and its citations are checked, but a sentence can still overstate or misattribute what a cited record says. Measuring unsupported claims is part of the evaluation process for every model change.
* **Completeness.** If NAM does not retrieve the record that contains the answer, NNLM cannot use it.

---

## How This Differs From Prompt-Based Grounding

| | Instructing a large model | NNLM |
|---|---|---|
| Irrelevant records | Present in the prompt; model asked to ignore them | Removed before the generator runs |
| No evidence | Model asked to admit it | Generator does not run |
| Citations | Requested | Required, and checked against what was shown |
| Past answers | Often in context | Never treated as evidence |

-> See also: [Record Selection](RECORD_SELECTION.md) | [Generation Model](GENERATION_MODEL.md) | [Design Principles](../PHILOSOPHY/DESIGN_PRINCIPLES.md)
