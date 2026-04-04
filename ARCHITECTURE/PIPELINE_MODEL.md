# Pipeline Model

This document describes what each stage of the NNLM pipeline does — its purpose, its inputs, its outputs, and its role in the system.

It is deliberately conceptual.
No model names, architectures, or training details are included.

---

## Stage 1: Encoding

### Purpose

Transform a natural language query into a structured query plan that NAM can execute.

### What It Does

The encoder takes a user's question and produces:

* **Entity identification** — which people, places, things, or concepts are mentioned
* **Entity resolution** — mapping surface forms to canonical identifiers, resolving pronouns and coreferences
* **Intent detection** — what kind of question is being asked (identity, location, relationship, etc.)
* **Query parameters** — the structured instructions NAM needs to perform retrieval
* **Quality verification** — checking that the query plan is well-formed before execution

The encoder does not guess. If it cannot resolve an entity or determine intent, it signals uncertainty — which affects downstream behavior.

### What It Produces

A query plan: a structured representation of what to ask NAM and how.

### Why It Matters

The quality of the query plan directly determines the quality of retrieval. A good query plan retrieves the right records. A bad query plan retrieves noise.

The encoder is where natural language meets structured memory. It is the bridge.

---

## Stage 2: Retrieval

### Purpose

Execute the query plan against NAM and return candidate records with rich metadata.

### What It Does

NAM receives the query plan and:

* navigates its semantic address space according to the plan
* returns records that occupy compatible regions of that space
* attaches metadata to each record: ontology type, match geometry, confidence indicators, entity associations

Retrieval is **broad by design**. NAM returns records that structurally match the query — but not all of them are necessarily useful for answering the specific question asked.

### What It Produces

A set of candidate records, each with:

* the record's content
* its position in NAM's address space
* metadata describing how and why it was retrieved
* the match geometry (point, line, or plane match)

### Why It Matters

Retrieval determines the evidence base. Everything downstream operates on what retrieval returns.

Because retrieval is deterministic, the evidence base is stable. The same query plan always produces the same records. This is the foundation of reproducibility.

---

## Stage 3: Selection

### Purpose

Separate signal from noise. Determine which retrieved records are actually relevant to the query.

### What It Does

The selection model examines each retrieved record and determines:

* Is this record relevant to the specific question asked?
* How strongly does it support an answer?
* Is it signal or noise?

Selection operates on the metadata from NAM — not just the text content of the records. It considers:

* ontology type alignment
* match geometry (how specifically the record matched)
* confidence indicators from retrieval
* characteristics of the record itself

Records that pass the selection threshold are forwarded to generation.
Records that do not are discarded.

If too few records pass selection, the pipeline produces a rejection response.

### What It Produces

A scored, filtered subset of the retrieved records — the evidence that the generation model will use.

### Why It Matters

Selection is the grounding mechanism. It ensures that the generation model only sees evidence that has been evaluated as relevant.

Without selection, the generator would need to determine relevance on its own — which language models are unreliable at.

Selection also enables rejection. If the evidence is insufficient, the system says so rather than hallucinating.

---

## Stage 4: Generation

### Purpose

Produce a natural language response from the selected records, with citations.

### What It Does

The generation model takes:

* the selected records and their content
* learned representations from the selection stage
* the original query

And produces:

* a natural language response
* citation markers linking claims to specific records
* or a rejection response if the evidence is insufficient

The generator is conditioned on the selection output — not prompted with raw text. It receives structured signals about what the evidence supports, not just documents to read.

### What It Produces

A cited natural language response, or a rejection.

### Why It Matters

Generation is where structure becomes language. The generator must be fluent enough to produce readable responses, but constrained enough to never assert without evidence.

This tension — fluency within constraints — is the core challenge of the generation stage.

---

## How the Stages Connect

```
User query
     |
     v
[Encoding]  -->  query plan
     |
     v
[Retrieval]  -->  candidate records + metadata
     |
     v
[Selection]  -->  scored, filtered records
     |
     v
[Generation]  -->  cited response (or rejection)
```

Each arrow represents:

* a clear data contract
* a measurable boundary
* an observable output

No stage has visibility into the internal workings of another.
Each stage operates on the output of the previous stage, nothing more.

---

## Contracts Between Stages

| From | To | Contract |
|---|---|---|
| Encoding | Retrieval | Structured query plan with entities, intent, and NAM parameters |
| Retrieval | Selection | Candidate records with full NAM metadata |
| Selection | Generation | Scored records that passed the relevance threshold |
| Generation | User | Cited natural language response or rejection |

These contracts are the system's invariants. If a contract is violated, the system fails visibly — not silently.

---

## What This Design Enables

* **Per-stage latency tracking** — know exactly where time is spent
* **Per-stage accuracy measurement** — evaluate each stage independently
* **Targeted improvement** — fix the stage that is failing, not the whole system
* **Independent training** — each model can be retrained without affecting others
* **Debuggable failures** — trace any wrong answer to the stage that caused it

---

## Summary

The NNLM pipeline is four stages, each with a clear purpose:

1. **Encoding** — understand the question
2. **Retrieval** — find the evidence
3. **Selection** — filter the evidence
4. **Generation** — say what the evidence supports

Each stage is independent. Each is measurable. Each has explicit contracts with its neighbors.

The pipeline is the architecture. The contracts are the guarantees.

-> See also: [Multi-Stage Design](MULTI_STAGE_DESIGN.md) | [Record Selection](RECORD_SELECTION.md) | [Generation Model](GENERATION_MODEL.md) | [Grounding Model](GROUNDING_MODEL.md)
