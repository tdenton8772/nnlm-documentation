# Design Principles

NNLM is defined less by which models it uses than by the rules its pipeline enforces. Each principle below names the mechanism that implements it today.

---

## 1. Grounding Over Fluency

An awkward answer that is supported by a cited record is preferred over a fluent one that is not.

**In practice:** the generator is a small encoder-decoder model trained to answer from supplied records. A larger, more fluent model was not chosen, because fluency without evidence is what NNLM exists to avoid.

---

## 2. Citation Over Assertion

Claims should name their source.

**In practice:** records are numbered in the generator's input; the generator emits `[N]` markers; any marker that does not point at a record the generator was shown is left out of the citations and logged.

---

## 3. Declining Over Guessing

"The records do not say" is a correct answer when the records do not say.

**In practice:** generation does not run when retrieval is judged insufficient, when nothing is retrieved, or when selection keeps nothing. The generator can also decline when the records it is shown lack the answer.

A lesson behind this principle: making a generator less inclined to decline, without giving it better evidence, made it invent more. Declining is the model's only honest signal that it has nothing to go on, so it is protected rather than trained away.

---

## 4. Structure Over Prompting

Guarantees come from the pipeline, not from instructions to a model.

**In practice:** irrelevant records are removed before generation rather than the generator being asked to ignore them; past answers are excluded from evidence rather than the model being asked not to repeat itself; the empty-selection gate stops generation rather than asking the model to admit ignorance.

---

## 5. Inspectable Stages

Every decision should be visible to the person debugging it.

**In practice:** planning, retrieval, selection and generation are separate services and separate calls. The UI shows the resolved question, every retrieved record with its provenance, every selector score, every guard that fired, which records were cited, the GPU's state, and how long each stage took. One-shot API responses also say where the decode ran (CPU or GPU).

---

## 6. Measurement Over Assumption

A change is an improvement only if it measures as one.

**In practice:** changes to models or configuration are judged against a fixed, graded question set and a standing bar, with the expected result stated before the run. Measured surprises have reversed intuitions more than once — keeping more records reduced unsupported claims instead of increasing them.

---

## 7. Graceful Degradation

NNLM answering slowly is acceptable; NNLM not answering is not.

**In practice:** everything runs on CPU. A GPU is used only after an explicit prewarm, and any GPU problem falls back to the local model for that call. Health checks stay responsive while long requests run.

---

## 8. NAM as Substrate

NNLM keeps no knowledge of its own. Facts live in NAM; conversation turns are written to NAM; provenance comes from NAM.

**In practice:** updating what NNLM knows means ingesting data into NAM, not retraining a model.

---

## Closing

These principles are what remain constant as models are replaced. A new selector or generator is acceptable only if the pipeline around it still enforces them.

-> See also: [Why NNLM Exists](../OVERVIEW/WHY_NNLM_EXISTS.md) | [Terminology](TERMINOLOGY.md) | [Multi-Stage Design](../ARCHITECTURE/MULTI_STAGE_DESIGN.md)
