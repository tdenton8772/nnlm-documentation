# Current State

What exists and runs. This is a factual inventory, not a plan.

---

## Maturity

NNLM runs end to end against a live NAM cluster: questions are planned, retrieved, selected, answered with citations, and stored as session turns. It is a working system under active development, not a managed service.

---

## Components

### UI

NNLM's chat interface, a Phoenix LiveView web app. It drives the pipeline stage by stage and renders each stage as it completes:

* a live stage strip (plan, retrieve, select, generate) while a question runs, with each finished stage's time; a stage skipped by a gate is marked skipped, not failed
* plan panel (resolved question, entities, scope, access patterns, query texts, graph hops), exportable as JSON or YAML
* retrieval and selection panel: every record with its selector score, kept/cited state, NAM match score and **NAM / VEC / GRAPH** provenance badges
* retrieval summary tiles for query, graph and session results; the graph count covers NNLM's own graph walk and NAM's graph path
* per-stage timings for every answer
* service status for the encoder, the decoder and NNLM's NAM query bridge
* GPU status (off, booting, loading, serving) with prewarm and release controls
* session sidebar; the current session survives reloads, and earlier sessions can be reopened

### Encoder service

* `/plan_stage`: reads session turns from NAM, resolves follow-up pronouns, calls the NAM encoder for entities and semantic signals, runs the trained planner (a T5 query planner), organises entities
* `/retrieve_stage`: NAM `/v1/query` per query text, optional graph walks, the trained quality planner with one broadening retry
* `/write_turn`: stores the turn in NAM's main bucket, routed to the data node that owns the key; failures are reported, not hidden
* `/sessions`, `/session/{id}/turns`: session listing and history
* `/ask`: one-shot answer for programmatic callers, optionally protected by a shared-secret header
* blocking routes run in a worker threadpool so health checks stay responsive

### Decoder service

* `/select_stage`: sentence encoder plus TabTransformer selector, keep rule (threshold or rank floor), grounding guards
* `/generate_stage`: T5 encoder-decoder over the kept records, citation check
* `/decode`: both stages in one call, reporting invalid citations and where it ran
* `/v1/gpu/status`, `/v1/gpu/prewarm`, `/v1/gpu/release`: GPU session control
* CPU models by default (the generator int8-quantized); a fallback ladder if the trained models fail to load

### GPU serving (optional)

A gRPC server image, booted on demand through an external GPU registry after an explicit prewarm from the decoder. It runs the decoder's sentence encoder and generator for the UI's staged requests, and the whole select-and-generate step for one-shot requests. Without a prewarm nothing is provisioned. The decoder holds a prewarmed GPU while it is in use, and it is released on request or after a period of inactivity. Whenever no GPU is serving, and on any GPU failure, calls run on CPU.

### NNLM NAM query bridge

A small auxiliary service that executes query plans against NAM with the same record normalisation as the encoder. The question pipeline does not route through it: the encoder calls NAM directly. The UI reports its health.

### NAM

Required, deployed separately. NNLM uses NAM's query service (fused address, vector and graph paths, graph walks, document access) and its data service for session turns, and runs NAM's encoder alongside its own services: NAM's rule-based parser and encoder heads, used read-only.

---

## Deployment

Containerised and deployed with a Helm chart to Kubernetes alongside NAM, with a values file per environment. NAM credentials are supplied through a Kubernetes secret (preferred) or at deploy time; the chart refuses to render with a missing or placeholder NAM credential.

---

## Models in Service

| Model | Role | Runs on |
|---|---|---|
| NAM encoder heads | Entity, ontology, affordance, attribute and context signals, from text parsed by NAM's rule-based parser | NAM encoder service (CPU) |
| T5 query planner (supervisor) | Scope, access patterns, graph hops, geometry | Encoder (CPU) |
| T5 quality planner | Sufficient, or broaden | Encoder (CPU) |
| Sentence encoder | Question and record embeddings for selection | Decoder (CPU) or prewarmed GPU |
| TabTransformer selector | Keep score per record | CPU: the decoder, or the GPU host for one-shot requests |
| T5 generator | Cited answer from kept records | Decoder (CPU, int8) or prewarmed GPU |

---

## Evaluation

Model and configuration changes are judged on a fixed, graded question set against a standing bar (answer correctness, attribution, unsupported entity mentions, decline correctness, and zero answers from parametric memory), with the expected result stated before each run.

---

## Training

The selector and the generator are trained offline and released as a matched pair. Nothing is learned at query time.

-> See also: [Current Limitations](../CAPABILITIES/CURRENT_LIMITATIONS.md) | [Near-Term Roadmap](NEAR_TERM.md) | [What NNLM Can Do](../CAPABILITIES/WHAT_NNLM_CAN_DO.md)
