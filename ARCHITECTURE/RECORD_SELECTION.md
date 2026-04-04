# Record Selection

Record selection is the **critical middle layer** between retrieval and generation.

It is the stage that makes grounding possible.
Without it, NNLM would be another RAG system — retrieving context and hoping the model uses it well.

---

## Why a Middle Layer Exists

NAM retrieval is broad by design.

When NAM navigates its semantic address space, it returns records that occupy compatible regions. This includes:

* records that directly answer the question
* records that are tangentially related
* records that share structural position but address different topics

This breadth is a feature. NAM does not pre-judge relevance — it provides the full neighborhood of semantic space that the query activates.

But a language model cannot handle this breadth well.

Given a mix of relevant and irrelevant records, a language model will:

* attempt to use all of them
* blend information from relevant and irrelevant sources
* generate claims that seem grounded but are drawn from noise
* produce confident, wrong answers

Record selection exists to prevent this.

---

## What Record Selection Does

Record selection takes the full retrieval result set and produces a **scored, filtered subset**.

For each retrieved record, it determines:

* **Relevance** — does this record address the specific query?
* **Signal strength** — how strongly does this record support an answer?
* **Keep or reject** — should this record be forwarded to generation?

Records that pass the threshold become the evidence base for generation.
Records that do not are discarded.

---

## What Signals Inform Selection

Record selection does not operate on text content alone.

NAM returns rich metadata with every record. Selection uses:

* **Ontology type** — what kind of entity or concept does this record describe?
* **Match geometry** — did the record match as a point (exact), line (partial), or plane (broad)?
* **Confidence indicators** — how specific was the match in NAM's address space?
* **Record characteristics** — structural properties of the record itself

These signals give the selection model far more information than raw text similarity.

A record that matches on all semantic axes with the correct ontology type is treated very differently from a record that matched broadly on a single axis.

---

## How Selection Enables Rejection

Rejection — the system's ability to say "I don't know" — is a direct consequence of record selection.

The mechanism is simple:

1. NAM returns candidate records
2. Selection scores each record
3. If no records (or too few) pass the selection threshold, the pipeline produces a rejection
4. The rejection is a first-class output, not an error

Without record selection, there is no principled way to reject.

A language model given *any* context will generate *something*. It has no concept of "insufficient evidence." Selection provides that concept.

---

## Why Selection Is a Learned Model

Record selection could be implemented as heuristic rules — threshold on match score, filter by ontology type, discard low-confidence matches.

But heuristics miss the interactions between signals.

A record with moderate confidence but perfect ontology alignment might be more relevant than a record with high confidence but wrong ontology type. A point match on a tangential axis might be less useful than a line match on the right axis.

These interactions are learned from training data, not hand-coded.

The selection model learns to combine NAM's rich metadata into a single relevance decision — one that reflects the actual relationship between query, record, and usefulness.

---

## What Selection Does Not Do

Record selection does not:

* **rewrite or transform records** — records pass through unchanged
* **rank records for the user** — selection is binary (keep or reject), not a ranking
* **generate any text** — selection produces scores, not language
* **make retrieval decisions** — retrieval is already complete before selection begins
* **learn at query time** — the selection model is trained offline and frozen at deployment

Selection is a filter. A sophisticated, learned filter — but a filter.

---

## The Threshold

The selection threshold determines how aggressive filtering is.

* A high threshold means fewer records pass, higher precision, more rejections
* A low threshold means more records pass, lower precision, fewer rejections

The threshold is configurable but fixed at deployment.
It does not adapt at runtime.

This is a deliberate choice. A fixed threshold means:

* behavior is predictable
* changes are explicit
* tuning is deliberate, not emergent

---

## How Selection Connects to Generation

The output of record selection is the **input to generation**.

This is not just a list of records. It includes:

* the records themselves
* the selection scores
* learned representations that encode what the selection model determined

The generation model receives these representations as structured conditioning — not as text pasted into a prompt. This is the soft prompt injection mechanism described in the generation model documentation.

---

## Why This Matters

Record selection is what separates NNLM from RAG.

In RAG:

```
Retrieve  ->  Generate
```

In NNLM:

```
Retrieve  ->  Select  ->  Generate
```

That additional stage is where grounding happens.
It is where rejection becomes possible.
It is where noise is removed before it can cause harm.

Record selection is not an optimization. It is the **core architectural contribution** of the NNLM pipeline.

---

## Summary

Record selection:

* sits between retrieval and generation
* separates signal from noise using NAM's rich metadata
* enables rejection when evidence is insufficient
* uses a learned model, not heuristic rules
* produces structured output that conditions the generation model
* is the mechanism that makes grounding architectural, not aspirational

Without record selection, NNLM would be retrieval plus generation.
With it, NNLM is a grounded language system.

-> See also: [Grounding Model](GROUNDING_MODEL.md) | [Generation Model](GENERATION_MODEL.md) | [Pipeline Model](PIPELINE_MODEL.md) | [Terminology](../PHILOSOPHY/TERMINOLOGY.md)
