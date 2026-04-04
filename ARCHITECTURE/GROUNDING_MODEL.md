# Grounding Model

NNLM's grounding is not a feature.
It is an **architectural constraint** that shapes every stage of the pipeline.

This document explains how NNLM ensures that every generated claim traces to a source — and why that guarantee is structural, not behavioral.

---

## What Grounding Means in NNLM

Grounding means:

> Every factual claim in a generated response is connected to a specific retrieved record that was scored as relevant by the selection stage.

This is not:

* a suggestion to the language model
* a post-processing verification step
* a quality metric applied after generation

It is a **pipeline invariant**.

---

## How Grounding Is Achieved

### 1. Deterministic Retrieval

All evidence comes from NAM.

NAM retrieval is:

* address-based, not similarity-based
* deterministic — the same query always returns the same records
* inspectable — every retrieval path can be traced

This means the evidence set is known, stable, and auditable before generation begins.

---

### 2. Noise Filtering Before Generation

NAM retrieval is intentionally broad. It returns records that occupy compatible semantic regions — but not all of those records are relevant to the specific query.

The record selection stage removes noise **before the generation model sees it**.

This matters because:

* language models are susceptible to distraction from irrelevant context
* noise records can cause hallucination even in well-prompted systems
* fewer, more relevant records produce more accurate generation

If the generation model never sees irrelevant records, it cannot be misled by them.

---

### 3. Conditioned Generation

The generation model does not receive a raw text prompt with retrieved documents pasted in.

Instead, it receives:

* the content of selected records
* learned representations from the selection stage that encode relevance signals
* the original query

This conditioning is more precise than text-based context stuffing. The model receives structured signals about what matters, not just more text to parse.

---

### 4. Citation as Structural Output

Citations in NNLM are produced **by the generation model** as part of its output structure.

They are not:

* added by post-processing
* inferred by matching text spans to documents
* optional formatting

The model is trained to produce citations as first-class output tokens. Every factual assertion is tagged with the record that supports it.

This means:

* citation correctness is measurable
* missing citations are detectable
* false citations are identifiable

---

## The Role of Record Selection in Preventing Hallucination

Record selection is the critical grounding mechanism.

Without it, the generation model would receive:

* relevant records
* partially relevant records
* irrelevant records that happen to share semantic space

And it would need to determine relevance on its own — which language models are bad at.

With record selection:

* the generation model receives only records scored as relevant
* the number of input records is reduced, limiting confusion
* the selection model's decisions are inspectable and measurable
* irrelevant records are explicitly removed, not relied upon to be ignored

Record selection transforms the grounding problem from "hope the model uses the right context" to "only give the model the right context."

---

## How Rejection Works

Grounding has a natural consequence: if there is insufficient evidence, the system should say so.

NNLM's rejection mechanism works as follows:

1. NAM retrieval returns a set of candidate records
2. The selection stage scores each record
3. If no records (or too few) pass the selection threshold, the pipeline produces a rejection response
4. The rejection response indicates that the system could not find sufficient evidence to answer

This is fundamentally different from a language model saying "I'm not sure" — which is a generated phrase, not a structural signal.

In NNLM, rejection means:

* the selection stage made a measurable decision
* the decision is based on concrete scores
* the threshold is configurable and auditable
* no hallucinated response was produced

---

## What Grounding Does Not Mean

Grounding in NNLM does not mean:

* **the response is correct** — if NAM contains incorrect records, the response will cite them faithfully
* **the response is complete** — if relevant records are not in NAM, they cannot be cited
* **the response is the only possible answer** — different retrieval paths might yield different evidence

Grounding means the response is **traceable to evidence**.
Whether that evidence is accurate is a separate concern — one that belongs to the data layer, not the language layer.

---

## Why This Is Different From RAG

In RAG:

* grounding is attempted by providing context
* there is no noise filtering
* the model decides what to use
* citation is cosmetic, not structural

In NNLM:

* grounding is enforced by pipeline architecture
* noise is removed before generation
* the model operates on pre-filtered evidence
* citation is a required output of the generation model

The difference is not degree. It is kind.

---

## Summary

NNLM's grounding model ensures that:

* every claim traces to a retrieved record
* irrelevant records are removed before generation
* citations are structural, not cosmetic
* rejection is preferred over hallucination
* the entire chain — from query to claim to record — is inspectable

Grounding is not what NNLM does.
It is what NNLM **is**.

-> See also: [Record Selection](RECORD_SELECTION.md) | [Generation Model](GENERATION_MODEL.md) | [Design Principles](../PHILOSOPHY/DESIGN_PRINCIPLES.md)
