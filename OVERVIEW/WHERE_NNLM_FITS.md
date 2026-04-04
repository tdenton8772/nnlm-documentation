# Where NNLM Fits

NNLM is not a chatbot.
It is not a search engine with a language model bolted on.
It is not RAG with better prompts.

It sits **between** retrieval systems and language models — and makes the connection structural.

---

## The Problem With Current Approaches

Most AI systems that generate natural language from data follow one of two patterns:

* **Closed-book generation** — the model answers from its training weights, with no external data
* **Open-book generation (RAG)** — the model is given retrieved context and asked to synthesize an answer

Both patterns share a fundamental weakness:

> The language model decides what to say.

NNLM exists to change that relationship.

---

## The Missing Layer

Modern AI stacks typically look like this:

```
Data  ->  Retrieval  ->  Language Model  ->  Output
```

What is missing is **grounding as a structural guarantee**.

NNLM inserts accountability between retrieval and generation:

```
Query
   |
Encoding (structured query planning)
   |
NAM Retrieval (deterministic, address-based)
   |
Record Selection (signal vs noise)
   |
Generation (constrained to selected records)
   |
Cited Response
```

Every stage is independent.
Every stage is measurable.
Every claim in the output traces to a source.

---

## NNLM vs Chatbots

### Chatbots

* generate from training weights
* no external grounding
* confidence without evidence
* hallucination is the default mode
* no citation mechanism

### NNLM

* generates from retrieved records only
* structurally grounded to NAM
* refuses to assert without evidence
* hallucination is architecturally prevented
* citation is a structural output, not a cosmetic addition

**Chatbots generate answers.
NNLM generates cited responses from retrieved evidence.**

---

## NNLM vs RAG

### RAG

* retrieves by embedding similarity
* stuffs context into a prompt
* hopes the model uses it correctly
* no structural guarantee of grounding
* no filtering between retrieval and generation

### NNLM

* retrieves by deterministic addressing via NAM
* filters noise records before generation
* conditions generation on learned representations, not just text
* every claim must cite a selected record
* rejection is a first-class output

**RAG is a pipeline.
NNLM is an architecture.**

They solve the same problem at different levels of rigor.

---

## NNLM vs Fine-Tuned Language Models

### Fine-tuned LLMs

* encode knowledge into model weights
* expensive to update
* opaque reasoning
* no traceability to source data
* drift as training data changes

### NNLM

* keeps knowledge in NAM, not in weights
* updates by ingesting new data into NAM
* traceable reasoning via citations
* deterministic retrieval path
* stable behavior across model versions

**Fine-tuned models learn facts.
NNLM looks them up.**

---

## Where NNLM Sits in the Landscape

NNLM occupies a specific position:

* **More grounded than RAG** — structural guarantees, not prompting conventions
* **More expressive than search** — natural language responses with citations
* **More auditable than chatbots** — every claim traces to evidence
* **More modular than fine-tuned models** — knowledge lives outside the model

It is most valuable when:

* accuracy matters more than fluency
* responses must be auditable
* hallucination is not an acceptable failure mode
* the knowledge base changes and the system must stay current

---

## What NNLM Is Not Trying To Be

NNLM is intentionally **not**:

* a general-purpose conversational AI
* a creative writing tool
* a summarization engine
* a replacement for NAM
* a standalone system (it requires NAM as its memory substrate)

Trying to make it those things would break its core guarantees.

---

## The Positioning in One Sentence

**NNLM is a structurally grounded language interface to deterministic memory, where every generated claim is constrained to cited evidence from NAM.**

---

## How It Fits Together

A typical interaction:

```
Natural language query  ->  NNLM encoder  ->  NAM retrieval  ->  record selection  ->  generation  ->  cited response
```

NAM provides the memory.
NNLM provides the language interface.
Each does what it is designed for.

They are co-dependent by design.

-> See also: [Why NNLM Exists](WHY_NNLM_EXISTS.md) | [Multi-Stage Design](../ARCHITECTURE/MULTI_STAGE_DESIGN.md) | [Why Not RAG](../PHILOSOPHY/WHY_NOT_RAG.md)
