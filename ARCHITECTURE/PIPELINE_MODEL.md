# Pipeline Model

This document walks through one question from the UI to a stored answer: what each stage does, what it receives, and what it returns.

---

## Services

| Service | Role |
|---|---|
| **UI** (Phoenix LiveView) | NNLM's chat interface; drives the stages in order and renders each one as it completes |
| **Encoder** (HTTP, Python) | Plan stage, retrieve stage, session turn read/write, one-shot `/ask` |
| **NAM encoder** (HTTP) | NAM's own feature extraction, run read-only: text is parsed with NAM's rule-based parser and passed through NAM's encoder heads, which return entities, ontology, affordance, attribute and context signals |
| **NAM query service** | NAM's `/v1/query` (fused address, vector and graph paths), graph walk, and document access |
| **Decoder** (HTTP, Python) | Record selection (TabTransformer) and generation (T5) |
| **GPU serving** (gRPC, optional) | Runs the decoder's sentence encoder and generator on a GPU after an explicit prewarm |

---

## Stage 1: Plan (`/plan_stage`)

**Input:** question text, optional history, session id.

1. **Load session turns.** If a session id is given, the encoder reads that session's stored turns from NAM by key.
2. **Resolve follow-up pronouns.** If the question contains a pronoun ("he", "her", "its", ...), one pronoun is replaced with the session's referent:
   * the referent is the subject of the most recent previous question (its longest capitalised name);
   * turns whose question itself contains a pronoun ("Who did he defeat?") are skipped, since they introduce no new subject;
   * after a "Who killed X?"-style question, which asks for someone other than the person it names, the referent is a name from that turn's answer;
   * possessives become `X's`.
3. **Extract features.** The resolved text goes to the NAM encoder, which parses it with NAM's rule-based parser and returns entities, ontology types, affordances, attributes and contexts.
4. **Plan.** A trained T5 planner (the "supervisor") reads the text, those features and the history, and decides scope, whether to walk the graph, how many hops, and search geometry. Questions that ask about relations ("related", "connected", "associated", "explore") are widened to an exploratory graph search.
5. **Organise entities.** Entities are de-duplicated and cleaned, and entity-plus-affordance query strings are built.

**Output:** the plan (scope, access patterns, query texts, graph seeds and hops, search geometry), the entities, the session turns, and stage timings. The output is JSON and is passed verbatim to the next stage, so the stages are stateless.

The UI renders the plan as soon as this returns.

---

## Stage 2: Retrieve (`/retrieve_stage`)

**Input:** the plan stage output.

1. **NAM query.** For each query text (the resolved question, the original question, and a few entity queries), the encoder calls NAM's `/v1/query`. NAM runs its address, vector and graph paths and returns one fused list. Each row carries its source document, NAM's address metadata (ontology, entity, match score, match geometry), a cosine score when the vector path contributed, and `sources` — every path that reached it.
2. **Normalise.** Each row becomes a record with `retrieved_via` set to those paths joined with `+` (for example `address+graph`). NAM's per-path trace (which paths ran, why, what each cost and contributed) is kept as `access_paths`.
3. **Filter.** Records stored by other conversation sessions and NNLM's own bookkeeping documents are dropped.
4. **Graph walk.** If the plan asks for it, the encoder walks NAM's graph from the leading entity seeds and adds the documents it reaches.
5. **Session scope.** The current session's key prefix is recorded so the decoder can tell this session's records from other sessions'.
6. **Quality check.** A second trained planner model (the "quality agent") judges whether the results are sufficient. If not, the stage retries once — adding a graph walk, or broadening the search geometry — and judges again.

**Output:** records, graph nodes, access-path trace, and a quality verdict: `sufficient`, `result_count`, and `graph_count` (records any graph path supplied: NNLM's own walk plus NAM's graph path), with `graph_via_nam` and `graph_only_via_nam` broken out. When NAM's graph path contributed, the plan's access patterns gain `nam_graph`.

The access-path trace is returned to the caller; it is not part of the decoder's input.

---

## Stage 3: Select (`/select_stage`)

**Input:** the question and the retrieval payload (records, quality verdict, session scope).

1. **Gate on quality.** If the quality verdict is insufficient, the stage returns a terminal "not enough information" answer and generation is skipped.
2. **Collect records.** The records with text, in a fixed order, up to the selector's fixed capacity. Records from other sessions, NNLM's bookkeeping documents and stored question-answer pairs (the system's own past output) are excluded here as well.
3. **Score.** A sentence encoder embeds the question and each record. The TabTransformer reads those embeddings plus NAM's categorical and numeric metadata for each record and outputs a keep score per record.
4. **Keep.** A record is kept if its score clears a fixed threshold *or* it ranks among a fixed number of the highest-scoring records.
5. **Guards.** If only echoes of earlier answers were kept while real source records exist, the best source record is added back. If nothing is kept, the answer is terminal and generation is skipped.

**Output:** a positional keep mask, per-record detail (score, kept, provenance, match score), the guard that fired if any, and a terminal answer when selection alone decided the outcome.

The UI renders the selection panel before generation starts.

---

## Stage 4: Generate (`/generate_stage`)

**Input:** the question, the payload, and the mask from selection.

1. **Render.** The question and the kept records are written as numbered lines (`[1] ...`, `[2] ...`), each truncated to a fixed length. Records that were not kept are not shown, so `[N]` numbers only what the generator sees.
2. **Generate.** The T5 model produces the answer with greedy decoding.
3. **Check citations.** Every `[N]` must refer to a record that was shown. A marker that does not is left out of the citation list, so it links to no record, and the decoder logs it.

**Output:** answer text, citations, cited record ids, and the selection detail with `cited` flags filled in. Each citation is mapped back to the record's position in the selection panel.

The one-shot `/decode` endpoint (used by `/ask`) also reports the markers it left out as `invalid_citations`, and where the decode ran (`execution`: `gpu` or `local`).

---

## Stage 5: Write turn (`/write_turn`)

After the answer is shown, the UI sends the question, the answer, the entities and the top records to the encoder, which stores the turn in NAM's main bucket under a key made from the session id and the turn number. The write is routed to the data node that owns the key. NAM then ingests the turn like any other document.

If the store rejects the write, `/write_turn` returns `ok: false` and logs it rather than reporting success. The UI does not wait for the write.

The answer's own people, organisations and places are stored first in the turn's entity list, so that after a "Who killed X?" question a follow-up pronoun can resolve to the person in the answer.

---

## Flow Summary

```
UI
 |-- /plan_stage      (encoder)  session turns, pronouns, NAM encoder, planner
 |-- /retrieve_stage  (encoder)  NAM /v1/query, graph walks, quality check
 |-- /select_stage    (decoder)  sentence encoder + TabTransformer + guards
 |-- /generate_stage  (decoder)  T5 over kept records, citation check
 |-- /write_turn      (encoder)  turn stored in NAM under the session id
```

Programmatic callers can use the encoder's **`/ask`** instead. It runs the plan and retrieve stages, then the decoder's combined select-and-generate step, in one call, and returns the answer, citations, selection detail and retrieval context. Given a session id, `/ask` reads that session's stored turns for pronoun resolution; it does not store a new turn.

---

## Responsiveness

Encoder and decoder routes that do blocking work run in the server's worker threadpool, so health checks stay responsive while a long query is in progress.

-> See also: [Multi-Stage Design](MULTI_STAGE_DESIGN.md) | [Record Selection](RECORD_SELECTION.md) | [Generation Model](GENERATION_MODEL.md) | [Session and Context](SESSION_AND_CONTEXT.md)
