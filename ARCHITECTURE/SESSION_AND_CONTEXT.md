# Session and Context

NNLM supports multi-turn conversation.

This is not achieved by stuffing previous turns into a context window.
It is achieved through **session-based memory** backed by NAM.

---

## The Context Window Problem

Most conversational AI systems handle multi-turn interaction by:

* concatenating previous turns into the prompt
* relying on the model's context window to maintain coherence
* hoping the model tracks references, pronouns, and topic shifts

This approach has fundamental limitations:

* context windows have size limits
* earlier turns get less attention as the conversation grows
* the model must re-process the entire conversation at every turn
* there is no structural representation of what was discussed — just raw text

NNLM takes a different approach.

---

## Session-Based Memory

In NNLM, each conversation is a **session**.

A session maintains:

* the history of queries in the conversation
* the entities that have been discussed
* resolved references from previous turns
* context that carries across turns

This information is managed through NAM's addressing and retrieval system — not by accumulating text in a prompt window.

---

## How Context Carries Across Turns

When a user asks a follow-up question, the encoder stage has access to the session context.

This means:

* **Pronoun resolution** — "What else did he do?" can be resolved to the entity discussed in the previous turn
* **Coreference tracking** — references to "the company" or "that project" can be linked to previously mentioned entities
* **Topic continuity** — the encoder understands that a follow-up question relates to the same subject
* **Entity persistence** — entities resolved in earlier turns remain available without re-identification

The encoder uses session context to produce more precise query plans for follow-up questions.

---

## How This Differs From Context Window Stuffing

| Property | Context window | Session memory |
|---|---|---|
| Storage | Raw text in prompt | Structured session state |
| Scalability | Limited by window size | Limited by session design |
| Resolution | Model must infer | Explicitly resolved |
| Efficiency | Re-processes everything | Incremental updates |
| Reliability | Degrades with length | Stable across turns |

Context window stuffing treats conversation history as undifferentiated text.
Session memory treats it as **structured knowledge** about what has been discussed.

---

## Pronoun Resolution and Coreference

Multi-turn conversations are full of implicit references:

* "Tell me more about **her**."
* "What happened **there**?"
* "How does **that** relate to the first one?"

In a system without session memory, these references are ambiguous.
The model must guess what "her," "there," and "that" refer to.

In NNLM:

* the encoder resolves pronouns against the session's entity history
* coreferences are resolved before the query plan is generated
* NAM retrieval receives a fully resolved query, not an ambiguous one
* resolution decisions are inspectable in the query plan

This produces more accurate retrieval and, consequently, more relevant responses.

---

## What Session Memory Does Not Do

Session memory does not:

* **persist across sessions** — each conversation starts fresh
* **learn from the conversation** — no model adaptation occurs during a session
* **expand NAM's knowledge** — session context is for retrieval, not ingestion
* **guarantee perfect resolution** — ambiguous references may still be unresolvable

Session memory is a **query enhancement mechanism**, not a knowledge accumulation mechanism.

---

## How Sessions Interact With the Pipeline

Sessions affect the **encoding stage** only.

The rest of the pipeline is unaware of sessions:

* **Retrieval** operates on the query plan, which already reflects session context
* **Selection** operates on retrieved records, independent of conversation history
* **Generation** operates on selected records, with the original query for context

This isolation is deliberate. Session context improves query planning without complicating downstream stages.

---

## Multi-Turn Example (Conceptual)

Turn 1:

* User: "What is Neural Addressable Memory?"
* Encoder identifies entity: NAM
* NAM retrieves relevant records
* Selection and generation produce a cited response

Turn 2:

* User: "Who created it?"
* Encoder resolves "it" to NAM from session context
* Query plan becomes: "Who created NAM?"
* Pipeline proceeds with a fully resolved query

Turn 3:

* User: "What are the design principles?"
* Encoder carries forward that the topic is NAM
* Query plan targets NAM design principles specifically

At no point is the full text of previous turns stuffed into a prompt.
The session provides structured context that makes each query self-contained.

---

## Summary

NNLM handles multi-turn conversation through:

* session-based memory, not context window stuffing
* explicit pronoun and coreference resolution in the encoder stage
* entity persistence across turns
* structured context that improves query planning
* isolation of session logic from downstream pipeline stages

Conversations in NNLM are coherent because the system **understands what has been discussed**, not because it re-reads the entire conversation at every turn.

-> See also: [Pipeline Model](PIPELINE_MODEL.md) | [Terminology](../PHILOSOPHY/TERMINOLOGY.md) | [What NNLM Can Do](../CAPABILITIES/WHAT_NNLM_CAN_DO.md)
