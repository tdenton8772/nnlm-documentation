# Current State

This document describes the **current state of the NNLM project today** — what exists, what works, and what is actively deployed.

It is intentionally factual.
This is not a roadmap, pitch, or vision statement.

---

## Project Maturity

NNLM is in an **active development and deployment phase** with a working end-to-end pipeline.

This means:

* the full pipeline — encoding, retrieval, selection, generation — is implemented and operational
* the system is deployed on cloud infrastructure
* queries produce cited, grounded responses from NAM
* per-stage measurement and debugging are functional
* the system handles multi-turn conversations with session context

The system is past prototype and into "does it produce trustworthy output in real conditions."

---

## Core Pipeline (Today)

### Multi-Agent Encoder

The encoding stage uses a multi-agent architecture with supervision.

Agents handle:

* entity identification and resolution
* query intent detection
* NAM query plan construction
* quality verification of the query plan

A supervisor coordinates the agents and produces the final query plan.

The encoder supports a multi-LLM fallback chain — if one language model is unavailable, the system falls back to alternatives without pipeline failure.

---

### NAM Retrieval

Retrieval is handled entirely by NAM.

NNLM sends the query plan to NAM and receives:

* candidate records
* rich metadata (ontology, geometry, match scores, entity associations)
* deterministic, reproducible results

NAM must always be running alongside NNLM. They are co-deployed by design.

---

### Trained Record Selection Model

The record selection stage uses a trained model that:

* scores each retrieved record for relevance
* separates signal from noise
* uses NAM metadata as input features (not just text)
* produces keep/reject decisions for each record

The model is trained offline on labeled data and frozen at deployment.

---

### Trained Generation Model

The generation stage uses a trained language model that:

* receives selected records and learned representations
* uses soft prompt injection from the selection stage
* produces cited natural language responses
* handles rejection when evidence is insufficient

The model is trained to produce citation markers as structural output.

---

## User Interface

NNLM includes a web interface built with Phoenix LiveView.

The interface provides:

* a conversational query interface
* real-time query plan inspection
* visibility into retrieved and selected records
* per-stage latency display
* session management for multi-turn conversations

The interface is designed for evaluation and debugging, not just end-user interaction.

---

## Deployment

NNLM is currently deployed on OCI (Oracle Cloud Infrastructure).

The deployment includes:

* the full NNLM pipeline (encoder, selection, generation)
* NAM (co-deployed, required)
* the web interface
* observability endpoints

Deployment is containerized and repeatable.

---

## Observability

NNLM provides per-stage observability:

* **Encoding** — query plan, identified entities, resolved references, agent decisions
* **Retrieval** — number of records returned, retrieval metadata, latency
* **Selection** — per-record scores, keep/reject decisions, threshold behavior
* **Generation** — response text, citations, generation latency
* **End-to-end** — total latency, pipeline success/failure

This makes it possible to trace any response back through the entire pipeline.

---

## Multi-LLM Fallback Chain

The encoder stage supports a configurable fallback chain of language models.

If the primary model is:

* unavailable
* rate-limited
* producing errors

The system automatically falls back to the next model in the chain.

This provides resilience without requiring manual intervention.

---

## What Has Been Validated

The current system has been validated with:

* multi-turn conversations with pronoun resolution
* queries across multiple NAM ontology types
* noise rejection and honest "I don't know" responses
* citation accuracy against selected records
* per-stage latency measurement
* query plan inspection and debugging
* session-based context carrying across turns

These are working capabilities, not theoretical designs.

---

## Documentation State

Public documentation currently focuses on:

* explaining the conceptual model
* defining terminology and principles
* setting expectations clearly
* enabling informed evaluation

Implementation details, model specifics, and training procedures live elsewhere.

---

## Summary

Today, NNLM is:

* a working end-to-end pipeline: encoding, retrieval, selection, generation
* deployed on cloud infrastructure with NAM as its memory substrate
* producing grounded, cited responses from deterministic memory
* supporting multi-turn conversations with session context
* observable and debuggable at every stage
* resilient via multi-LLM fallback
* accessible through a functional web interface with query plan inspection

It is early — but it is **real, working, and grounded**.

-> See also: [Current Limitations](../CAPABILITIES/CURRENT_LIMITATIONS.md) | [Near-Term Roadmap](NEAR_TERM.md) | [What NNLM Can Do](../CAPABILITIES/WHAT_NNLM_CAN_DO.md)
