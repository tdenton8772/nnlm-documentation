# Where NNLM Fits

NNLM sits between a memory system (NAM) and the person asking a question. Its job is to turn retrieved records into a short answer that only says what those records support.

---

## The Usual Patterns

* **Closed-book generation.** A model answers from its weights. No sources, no way to check.
* **Retrieval-augmented generation (RAG).** Retrieve passages, paste them into a large model's prompt, let the model decide what to use.

In both, the language model decides what is relevant and what to say, in one opaque step.

NNLM splits that step apart.

---

## The NNLM Shape

```
Question (+ session id)
   |
Plan stage      -- resolve follow-up pronouns from stored turns, extract entities,
   |               trained planner chooses access patterns
Retrieve stage  -- NAM query (address + vector + graph paths), graph walks,
   |               trained quality check, one broadening retry if needed
Select          -- TabTransformer scores each record; keep the evidence
   |
Generate        -- T5 writes an answer from the kept records, citing [N]
   |
Write turn      -- the exchange is stored back in NAM under the session id
```

In the UI each arrow is a separate HTTP call with a JSON contract, and each stage's output is shown as it completes.

---

## NNLM vs Chat Assistants

| | Chat assistant | NNLM |
|---|---|---|
| Knowledge source | Model weights | Records in NAM |
| Sources shown | Rarely, or after the fact | Every answer, per claim |
| When it does not know | Usually answers anyway | Declines |
| Scope | Anything | Questions the stored records can answer |

---

## NNLM vs RAG

NNLM does use similarity search: NAM's vector path is one of its three retrieval paths. What differs is what happens next.

| | Typical RAG | NNLM |
|---|---|---|
| Retrieval | Embedding similarity | NAM's fused address, vector and graph paths, with per-record provenance |
| Between retrieval and generation | Nothing, or a reranker | A trained selector that keeps or drops each record |
| Generator input | Everything retrieved | Only the kept records, numbered |
| No evidence kept | Generates anyway | Generation does not run |
| Citations | Optional, unchecked | Required; markers naming records not shown are not counted |
| Generator size | Large hosted model | Small encoder-decoder, runs on CPU |

---

## NNLM vs Fine-Tuned Models

A fine-tuned model stores knowledge in its weights; updating the knowledge means re-training. NNLM's models learn how to *select and phrase*, not facts. Updating what NNLM knows means ingesting new data into NAM.

---

## Where It Is Most Useful

* when answers must be checkable against source records
* when the knowledge base changes often
* when hardware is limited
* when declining is acceptable and guessing is not

---

## What NNLM Is Not Trying To Be

* a general-purpose assistant
* a creative or summarisation tool for arbitrary text
* a replacement for NAM, or a system that works without it

---

## In One Sentence

**NNLM is a staged, inspectable language interface to NAM that only answers from records it has selected as evidence.**

-> See also: [Why NNLM Exists](WHY_NNLM_EXISTS.md) | [Multi-Stage Design](../ARCHITECTURE/MULTI_STAGE_DESIGN.md) | [Why Not RAG](../PHILOSOPHY/WHY_NOT_RAG.md)
