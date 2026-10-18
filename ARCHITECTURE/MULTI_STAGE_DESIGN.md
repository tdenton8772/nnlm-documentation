# Multi-Stage Design

NNLM is not one model. It is a sequence of stages, each with its own job, its own model (or none), and its own output that can be inspected.

---

## Why Stages

A single model that takes a question and returns an answer hides every decision it made. When the answer is wrong, the only diagnosis available is "the model".

NNLM separates the decisions so each can be seen, measured and improved on its own:

| Decision | Stage | Model |
|---|---|---|
| Who is the question about, given the conversation? | Plan | Rule-based pronoun resolution over stored turns; NAM's encoder heads for entities |
| How should NAM be searched? | Plan | Trained T5 query planner (supervisor) |
| What does NAM have? | Retrieve | NAM (address, vector and graph paths) plus optional graph walks |
| Is that enough to try answering? | Retrieve | Trained T5 quality planner, with one broadening retry |
| Which records are evidence? | Select | Sentence encoder + TabTransformer, plus fixed guards |
| What do the records say? | Generate | Fine-tuned T5 encoder-decoder, plus a citation check |

---

## Stage Contracts

Stages talk over HTTP with JSON. The plan stage's output is the retrieve stage's input, verbatim; the decoder payload is built from the retrieve stage's output; the select stage's mask is the generate stage's input. No stage keeps request state between calls.

| From | To | Carries |
|---|---|---|
| Plan | Retrieve | Plan (scope, access patterns, query texts, graph seeds, hops, geometry), entities, session turns |
| Retrieve | Select | Records with text, NAM metadata and `retrieved_via`; quality verdict; the current session's scope |
| Select | Generate | Positional keep mask, per-record detail, guard, or a terminal answer |
| Generate | UI / caller | Answer, citations, cited record ids, selection detail (one-shot responses add invalid citations and execution location) |

A terminal answer from selection (insufficient quality, no records, nothing kept) means generation does not run. The UI shows the generate stage as *skipped*, not failed.

---

## What Stages Make Possible

**Visible progress.** The UI renders the plan while retrieval runs and the selection panel while generation runs.

**Targeted diagnosis.** For a wrong answer you can ask, in order:

1. Was the question resolved correctly? (resolved text, entities)
2. Did NAM return a record containing the answer? (retrieved records, provenance)
3. Did selection keep it? (per-record scores and keep flags)
4. Did the generator use it? (citations, invalid citations)

When the answer-bearing record was never retrieved, the failure is in retrieval, not in the language models, and the stage split is what makes it visible as one.

**Independent improvement.** The selector can be retrained without touching the generator, the generator without touching retrieval, and the planner without touching either.

**Independent placement.** The decoder's two most expensive models (sentence encoder and T5) can run on a prewarmed GPU while the planners and the rest stay on CPU; see [Generation Model](GENERATION_MODEL.md#where-it-runs).

---

## The Cost

* more services to deploy and keep compatible
* model contracts that must match between training and serving (for example, the selector must be served the same features, and the generator the same input layout, it was trained on)
* more latency than one call to one model

These costs are accepted because the alternative hides failure.

---

## Summary

Each stage makes one decision, exposes it, and hands a JSON contract to the next. That is what lets NNLM show its work and lets failures be traced to the stage that caused them.

-> See also: [Pipeline Model](PIPELINE_MODEL.md) | [Design Principles](../PHILOSOPHY/DESIGN_PRINCIPLES.md) | [Grounding Model](GROUNDING_MODEL.md)
