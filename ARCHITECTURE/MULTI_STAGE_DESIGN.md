# Multi-Stage Design

NNLM is not a single model.
It is a **pipeline of independent stages**, each with its own purpose, its own model, and its own failure modes.

This is a deliberate architectural choice.

---

## Why Multi-Stage

Most AI systems that generate language are monolithic:

* one model takes input and produces output
* reasoning is internal and opaque
* failures are invisible until the output is wrong
* improvement requires retraining the entire system

NNLM rejects this approach.

Instead, the pipeline is decomposed into stages that can be:

* independently trained
* independently evaluated
* independently debugged
* independently improved

This costs more engineering effort.
It produces a more trustworthy system.

---

## The Four Stages

### 1. Encoding

The encoding stage transforms a natural language query into a structured query plan.

It answers:

* What entities are mentioned?
* What is the user asking about?
* How should NAM be queried?
* What retrieval strategy should be used?

The encoder does not retrieve information. It produces **instructions** for retrieval.

---

### 2. Retrieval

The retrieval stage executes the query plan against NAM.

It returns:

* records that occupy compatible regions of NAM's semantic space
* rich metadata for each record (ontology, geometry, match scores)
* a deterministic result set

Retrieval is broad by design. It returns everything that *might* be relevant, not just what *is* relevant.

The same query plan always produces the same retrieval results.

---

### 3. Selection

The selection stage separates signal from noise.

It takes the full retrieval result set and determines:

* which records are actually relevant to the query
* which records are noise (structurally compatible but not useful)
* how strongly each record supports an answer

Selection is a learned model that operates on NAM's metadata — ontology types, match geometry, confidence scores, and record characteristics.

This stage is what makes rejection possible. If no records pass the selection threshold, the system declines to answer.

---

### 4. Generation

The generation stage produces a natural language response from the selected records.

It is conditioned on:

* the selected records and their content
* learned representations from the selection stage
* the original query

It is constrained to:

* only make claims supported by selected records
* cite every factual assertion
* produce rejection responses when selection yields insufficient evidence

The generation model does not have unconstrained access to its training knowledge. It operates within the boundary set by the selection output.

---

## Why Each Stage Has Its Own Model

A monolithic model hides decisions.
A multi-stage pipeline makes them visible.

| Property | Monolith | Multi-stage |
|---|---|---|
| Failure isolation | Impossible | Per-stage |
| Debugging | Guesswork | Inspectable |
| Improvement | Retrain everything | Improve one stage |
| Measurement | End-to-end only | Per-stage metrics |
| Trust | "The model said so" | "Here is why" |

Each stage has:

* a clear input contract
* a clear output contract
* measurable performance
* independent training data

---

## How This Enables Measurement

Because each stage has observable outputs, NNLM supports:

* **Encoding measurement** — was the query plan correct? Were entities resolved properly?
* **Retrieval measurement** — did NAM return relevant records? How many? From which regions?
* **Selection measurement** — were the right records kept? Were noise records filtered?
* **Generation measurement** — did the output cite correctly? Did it hallucinate?
* **Latency measurement** — how long did each stage take?

This is not observability as an afterthought.
It is observability as a design consequence.

---

## How This Enables Debugging

When an NNLM response is wrong, the multi-stage design lets you ask:

1. Was the query plan correct? (Check the encoder output)
2. Did NAM return the right records? (Check the retrieval output)
3. Were the right records selected? (Check the selection scores)
4. Did the generator use them correctly? (Check the citations)

In a monolithic system, the only question you can ask is "why was the output wrong?" — and the answer is always "the model."

---

## How This Enables Improvement

Each stage can be improved independently:

* Better entity resolution? Improve the encoder.
* Missing records? Improve NAM's coverage or addressing.
* Too much noise? Improve the selection model.
* Awkward phrasing? Improve the generation model.

No single improvement requires retraining the entire system.

---

## The Cost of Multi-Stage

This design is not free.

It requires:

* more engineering to build and maintain
* clear contracts between stages
* coordination of training across stages
* higher latency than a single model call

These costs are real. They are accepted because the alternative — an opaque monolith — is worse for the problems NNLM is designed to solve.

---

## Summary

NNLM's multi-stage design exists because:

* trust requires transparency
* transparency requires measurability
* measurability requires stage separation
* stage separation requires explicit contracts

The pipeline is the architecture. The architecture is the guarantee.

-> See also: [Pipeline Model](PIPELINE_MODEL.md) | [Design Principles](../PHILOSOPHY/DESIGN_PRINCIPLES.md) | [Grounding Model](GROUNDING_MODEL.md)
