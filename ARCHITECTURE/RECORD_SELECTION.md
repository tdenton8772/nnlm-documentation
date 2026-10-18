# Record Selection

Record selection is the stage between retrieval and generation. It decides which retrieved records the generator is allowed to see.

---

## Why It Exists

NAM retrieval is broad. A query returns records that match on some of the question's semantic axes, records that are close in embedding space, and records reached through the graph. Some contain the answer. Many do not.

A generator handed all of them tends to blend them, cite the wrong one, or answer from a near-miss. Selection removes records before the generator sees them, so the generator cannot be misled by what it was never shown.

---

## What the Selector Sees

For each record (up to a fixed number per question):

* **Text meaning.** The question and the record are each embedded with a pretrained sentence encoder.
* **NAM categorical metadata.** Query mode, affordance, record source, ontology type, match geometry.
* **NAM numeric signals.** The address engine's match score (how many semantic axes matched), the vector path's cosine similarity, and graph hop count. These are kept as separate inputs because they are on different scales.
* **Position** in the result list.

The selector is a **TabTransformer**-style model: each record's categorical metadata is embedded and combined with the question and record embeddings and the numeric signals, then attention runs across the records of one question, so each record is scored in the context of the others. It outputs one keep score per record.

---

## The Keep Rule

A record is kept if either:

* its score clears a fixed threshold, or
* it ranks among a fixed number of the highest-scoring records.

The two are combined as a union, so a record the model is confident about is never dropped for falling outside the rank floor. The rank floor exists because a threshold alone was dropping the answer-bearing record too often, even when the selector had ranked it highly; keeping more records was measured to improve answers and reduce unsupported claims rather than add noise.

Both values are fixed per deployment, not adjusted per query.

---

## Guards

After scoring, two fixed rules apply:

* **Source floor.** Stored conversation turns are paraphrases of earlier answers. If the selector kept only those while real source records exist, the best-scoring source record is added back, so a source cannot be crowded out by an echo of itself.
* **Empty gate.** If nothing is kept, generation does not run and the answer is a decline. The generator never writes with nothing to ground on.

Before scoring, records from other conversation sessions, NNLM's own bookkeeping documents, and stored question-answer pairs (the system's own past output) are removed. A previous answer is never treated as evidence.

---

## What Selection Returns

* a keep mask over the records, in a fixed order the generate stage relies on
* per-record detail: selector score, kept, raw keep (before guards), retrieval provenance, match score
* the guard that fired (`none`, `source_floor`, `gated_empty`, `insufficient_quality`, `no_records`)
* a terminal answer when selection alone decided the outcome

The UI shows this panel before generation starts.

---

## What Selection Does Not Do

* It does not rewrite records.
* It does not retrieve; it only filters what retrieval returned.
* It does not learn at query time. The selector is trained offline and frozen in the deployed image.

---

## Selector and Generator Are a Pair

The selector is released together with the generator it was evaluated alongside. A selector that scores better on its own can make the end-to-end answers worse in front of a different generator, so the two move together or not at all.

---

## Known Gaps

* A few selector inputs are fixed at serving time rather than filled from the payload. Closing these train/serve differences is ongoing work.
* Selection can only choose among what retrieval returned. When NAM does not retrieve an answer-bearing record, selection cannot recover it.

-> See also: [Grounding Model](GROUNDING_MODEL.md) | [Generation Model](GENERATION_MODEL.md) | [Pipeline Model](PIPELINE_MODEL.md)
