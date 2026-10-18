# Frequently Asked Questions

Short answers to the questions engineers and evaluators ask most often.

---

## Is this a chatbot?

No. NNLM answers questions from records in NAM, with citations, and declines when the records do not contain the answer. It handles follow-up questions in a conversation, but not small talk, opinions or creative requests.

---

## Is this RAG?

It retrieves before it generates, so broadly yes. It differs from the common pattern in that a trained selector decides which retrieved records the generator may see, generation does not run without evidence, the generator is a small encoder-decoder model, and citations are checked against what the generator was shown.

-> See: [Why Not RAG](../PHILOSOPHY/WHY_NOT_RAG.md)

---

## Does it use vector search?

Yes, as one of three paths. NAM's query service fuses semantic-address matching, vector similarity and a graph index, and tags each record with the paths that reached it. The UI shows these as NAM, VEC and GRAPH badges.

---

## Which models does it use?

* NAM's encoder, for entities and semantic signals: NAM's rule-based parser followed by its encoder heads
* two small T5 planner models (one plans retrieval, one judges whether results are sufficient)
* a sentence encoder and a TabTransformer for record selection
* a fine-tuned T5 encoder-decoder for generation

No hosted large language model is used in the primary path.

---

## Why an encoder-decoder instead of a big chat model?

Its answers are conditioned on the selected records rather than on parametric memory; it is small enough to run on CPU; records are numbered in its input so citations are structural and checkable; and it can say when the evidence is insufficient.

---

## Does it hallucinate?

It is built to make that hard: irrelevant and past-answer records are removed before generation, generation is gated on evidence, and markers naming unseen records are not counted as citations. It is still a trained model and can misstate what a cited record says. Every candidate model is measured for unsupported claims and answers drawn from parametric memory before it ships.

---

## Can it say "I don't know"?

Yes. Structurally, when retrieval is insufficient or selection keeps nothing (the generator is not run). And from the generator, which can decline when the records it is shown do not contain the answer.

---

## Why did it decline?

The panels show why. The retrieval panel shows what NAM returned, so you can see whether a record containing the answer was there; the selection panel shows whether it was kept. A record that was never retrieved cannot be used, however good the selector and generator are.

---

## Is it deterministic?

Selection and generation are: the selector applies a fixed rule to its scores, and generation uses greedy decoding. CPU and GPU runs use different numeric precision, so scores and wording can differ slightly between them. Retrieval reflects NAM's current state, which changes as data — including new session turns — is ingested.

---

## Does it need a GPU?

No. Everything runs on CPU. A GPU can be prewarmed from the UI to speed up the decoder's sentence encoder and generator. It is held through the registry while in use and released on request or after a period of inactivity, and any GPU failure falls back to CPU for that call.

---

## How does it handle follow-up questions?

Each turn is stored in NAM under the session id. Before retrieval, a pronoun in the new question is replaced with the previous question's subject, or with a name from the answer after a "Who killed X?"-style question.

-> See: [Session and Context](../ARCHITECTURE/SESSION_AND_CONTEXT.md)

---

## Can it work with custom data?

Yes. Data ingested into NAM is available to NNLM without retraining, because knowledge lives in NAM rather than in model weights. Adapting the selector and generator to a very different domain is separate, offline work; see [Long-Term Direction](../ROADMAP/LONG_TERM.md).

---

## What if NAM is wrong?

NNLM will repeat the wrong record, with a citation that lets you find it. Grounding means traceable, not true.

---

## Can other systems call it?

Yes. The encoder's `/ask` endpoint runs the whole pipeline and returns the answer, citations, selection detail, retrieval context and where the decode ran. It can be protected with a shared-secret header.

---

## Is it production-ready?

It runs end to end on Kubernetes alongside NAM, with health checks, a CPU fallback for every GPU call, and per-stage visibility. Retrieval for natural-language questions, generator quality and integration with external monitoring are the main areas of active work; see [Near-Term Roadmap](../ROADMAP/NEAR_TERM.md).

-> See: [Current State](../ROADMAP/CURRENT_STATE.md) | [Current Limitations](../CAPABILITIES/CURRENT_LIMITATIONS.md)
