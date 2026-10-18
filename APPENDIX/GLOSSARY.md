# Glossary

A quick-reference index. Canonical definitions live in [Terminology](../PHILOSOPHY/TERMINOLOGY.md).

---

**Access paths** — NAM's address, vector and graph retrieval paths inside one query. -> Terminology

**Citation** — an `[N]` marker naming a record the generator was shown; checked after generation. -> Terminology

**Decoder** — the service that runs record selection and generation. -> [Pipeline Model](../ARCHITECTURE/PIPELINE_MODEL.md)

**Decline** — an answer stating the records do not support a response; structural (a gate fired) or generated. -> Terminology

**Encoder** — the service that runs the plan and retrieve stages, session read/write and `/ask`. -> [Pipeline Model](../ARCHITECTURE/PIPELINE_MODEL.md)

**Execution** — where a one-shot decode was computed: `gpu` or `local`. -> Terminology

**Generation** — the T5 encoder-decoder writing a cited answer from the kept records. -> [Generation Model](../ARCHITECTURE/GENERATION_MODEL.md)

**Grounding** — the traceable link between a claim and a shown record. -> [Grounding Model](../ARCHITECTURE/GROUNDING_MODEL.md)

**Guard** — a fixed post-selection rule: source floor or empty gate. -> [Record Selection](../ARCHITECTURE/RECORD_SELECTION.md)

**Invalid citation** — a marker naming no shown record; not counted as a citation, and reported. -> Terminology

**Match geometry / match score** — NAM metadata describing how a record matched in address space. -> Terminology

**NAM** — Neural Addressable Memory, NNLM's memory substrate. -> Terminology

**Noise record** — a retrieved record that does not help answer; removed by selection. -> Terminology

**Poisoned record** — a record never allowed as evidence: other sessions' turns, bookkeeping, stored question-answer pairs. -> Terminology

**Prewarm** — the explicit request that provisions a GPU for the decoder; without it, everything runs on CPU. -> Terminology

**Provenance / `retrieved_via`** — the NAM paths that reached a record; shown as NAM, VEC, GRAPH badges. -> Terminology

**Quality agent** — trained planner that judges whether retrieval is sufficient. -> Terminology

**Query plan** — the plan stage's output. -> Terminology

**Record selection / selector** — the TabTransformer stage that decides which records the generator sees. -> [Record Selection](../ARCHITECTURE/RECORD_SELECTION.md)

**Referent** — the entity a follow-up pronoun resolves to. -> [Session and Context](../ARCHITECTURE/SESSION_AND_CONTEXT.md)

**Render** — the generator's text input: question plus numbered kept records. -> Terminology

**Session / turn** — a conversation and its stored exchanges in NAM. -> [Session and Context](../ARCHITECTURE/SESSION_AND_CONTEXT.md)

**Supervisor** — trained planner that chooses scope, access patterns and graph hops. -> Terminology

---

New terms are added here only after they are defined in Terminology.
