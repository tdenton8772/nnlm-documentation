# Why Not RAG

NNLM is not an improved RAG system.
It is a **different architecture** that solves the same problem RAG was designed for — but with structural guarantees that RAG cannot provide.

This document explains why.

---

## What RAG Does Well

RAG was a genuine advance. It addressed a real problem:

> Language models hallucinate because they generate from training weights, not from evidence.

RAG's solution: retrieve relevant documents and include them in the prompt.

This helped. Models became more grounded when given relevant context.

But RAG's improvements are **behavioral, not structural**.

---

## The Structural Problems With RAG

### 1. Retrieval Is Similarity-Based

RAG retrieves documents by embedding similarity.

This means:

* retrieval quality depends on embedding model quality
* results change when the embedding model changes
* "similar" is not the same as "relevant"
* there is no structural guarantee that the right documents are retrieved

NNLM uses NAM for retrieval. NAM retrieves by deterministic semantic addressing, not similarity scoring. The same query always returns the same records.

---

### 2. There Is No Noise Filtering

RAG passes all retrieved documents directly to the language model.

This means the model receives:

* relevant documents
* partially relevant documents
* irrelevant documents
* documents that look similar but answer different questions

The model must figure out which documents to use. It often gets this wrong.

NNLM inserts a record selection stage between retrieval and generation. Noise records are identified and removed. The generation model only sees records that have been scored as relevant.

---

### 3. Grounding Is Not Enforced

RAG provides context to the language model.
It does not **require** the model to use that context.

The model is free to:

* ignore the retrieved documents
* blend retrieved information with training data
* generate claims that sound like they came from context but did not
* hallucinate confidently while appearing grounded

NNLM's generation stage is structurally conditioned on selected records. Citation is a required output, not a suggested behavior.

---

### 4. Context Stuffing Has Limits

RAG works by inserting retrieved text into the prompt window.

This creates problems:

* context window limits constrain how much can be provided
* more context does not mean better answers — it often means more confusion
* the model must locate relevant information within a wall of text
* position bias means documents at the beginning and end get more attention

NNLM does not stuff text into a context window. The generation model receives learned representations from the selection stage — structured signals, not raw text. This is more information-dense and more reliable than context stuffing.

---

### 5. No Rejection Mechanism

RAG systems almost always produce an answer.

If the retrieved documents are irrelevant, the model generates anyway — from its training data, from partial context, from invention.

There is no structural mechanism to say "the evidence is insufficient."

NNLM has rejection as a first-class output. When record selection determines that no retrieved records sufficiently address the query, the system declines to answer rather than hallucinate.

---

### 6. No Per-Stage Measurement

RAG is typically a two-stage process: retrieve, then generate.

If the output is wrong, you cannot easily determine:

* Was retrieval bad?
* Was the model's use of context bad?
* Did the model hallucinate despite good context?
* Was the question ambiguous?

NNLM has four independent stages, each with measurable inputs and outputs. When something goes wrong, you can identify *which stage* failed and *why*.

---

### 7. Debugging Is Guesswork

When a RAG system produces a wrong answer:

* you can inspect what was retrieved
* you cannot inspect why the model chose to use (or ignore) specific documents
* you cannot replay the model's reasoning
* you cannot isolate the failure to a specific decision

In NNLM:

* the query plan is inspectable
* retrieved records are visible with full NAM metadata
* selection scores show why each record was kept or rejected
* the generation model's input is fully determined by the selection output
* citation markers identify exactly which record supports each claim

---

## The Fundamental Difference

RAG treats retrieval and generation as **adjacent steps**.

NNLM treats them as **structurally coupled stages** with explicit contracts between each.

In RAG:

```
Retrieve  ->  [hope the model uses it]  ->  Generate
```

In NNLM:

```
Encode  ->  Retrieve  ->  Select  ->  [conditioned generation]  ->  Cited output
```

The difference is not quality. It is **architecture**.

---

## When RAG Is Fine

RAG is appropriate when:

* approximate grounding is acceptable
* hallucination is an inconvenience, not a danger
* per-response auditability is not required
* the knowledge base is small and well-curated
* debugging individual responses is not a priority

NNLM exists for environments where those conditions do not hold.

---

## What NNLM Is Not Claiming

NNLM does not claim:

* RAG is useless
* embedding-based retrieval has no value
* all language models are untrustworthy

NNLM claims one thing:

> **If grounding must be a guarantee rather than a best effort, you need architectural enforcement, not retrieval augmentation.**

---

## In One Line

**RAG suggests grounding. NNLM enforces it.**

-> See also: [Where NNLM Fits](../OVERVIEW/WHERE_NNLM_FITS.md) | [Grounding Model](../ARCHITECTURE/GROUNDING_MODEL.md) | [Design Principles](DESIGN_PRINCIPLES.md)
