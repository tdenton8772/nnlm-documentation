# Why Not RAG

NNLM retrieves before it generates, so in the broadest sense it is retrieval-augmented. This document explains how it differs from the common RAG pattern and why those differences matter.

---

## The Common Pattern

1. Embed the question.
2. Fetch the top-k most similar passages.
3. Paste them into a large model's prompt with instructions to use them and cite them.
4. Return whatever the model writes.

This improved on answering from weights alone. Its weaknesses are structural: the model sees everything retrieved, decides relevance itself, may blend in what it already "knows", and is only *asked* to cite.

---

## Where NNLM Differs

### 1. Retrieval is multi-path, with provenance

Similarity search is part of NNLM's retrieval: NAM's vector path is one of three. NAM also matches records on semantic address axes and reaches records through a graph index, then fuses the paths and tags every record with the paths that reached it. NNLM can add its own graph walks. The generator's evidence therefore comes with a record of *how* it was found.

### 2. A trained selector sits between retrieval and generation

A TabTransformer scores every retrieved record from its text and NAM's metadata. Only kept records reach the generator. In the common pattern there is nothing here, or a reranker whose output is still all passed through.

### 3. Generation can be refused

If retrieval is judged insufficient, or nothing is kept, the generator does not run. A model that is always called will always produce something.

### 4. The generator sees only evidence

Dropped records are not in the generator's input at all. It cannot be distracted by them and cannot cite them.

### 5. Citations are checked

Every `[N]` must point at a shown record. Those that do not are not counted as citations, and are logged.

### 6. Past answers are not evidence

Stored conversation turns are excluded from evidence. In a typical chat RAG, earlier model output sits in the context and can be repeated as fact.

### 7. The generator is small

A fine-tuned T5 encoder-decoder, running on CPU, trained to answer from the records it is given. Retrieval and selection do the work a large model is otherwise trusted to do implicitly.

### 8. Every stage is visible

Plan, retrieval (with provenance), selection (with scores) and generation (with citations) are separate outputs. When an answer is wrong, you can see which stage failed.

---

## Side by Side

```
Common RAG:   question -> similar passages -> large model -> answer

NNLM:         question -> plan (session-aware) -> NAM: address + vector + graph
                       -> quality check -> selector keeps evidence
                       -> small generator over kept records -> citation check
                       -> answer, or decline
```

---

## When the Common Pattern Is Fine

* approximate grounding is acceptable
* a large hosted model is available and affordable
* per-answer auditability is not required

NNLM is for cases where those do not hold.

---

## What NNLM Does Not Claim

* That similarity search is bad. NAM uses it.
* That NNLM's answers are always right. They are traceable; correctness depends on retrieval and on the source records.
* That structure removes all model error. It removes classes of error (unseen-record citations, generation without evidence, past answers as facts) and makes the rest visible.

---

## In One Line

**RAG gives a model evidence and asks it to behave; NNLM chooses the evidence, withholds the rest, and checks the citations.**

-> See also: [Where NNLM Fits](../OVERVIEW/WHERE_NNLM_FITS.md) | [Grounding Model](../ARCHITECTURE/GROUNDING_MODEL.md) | [Design Principles](DESIGN_PRINCIPLES.md)
