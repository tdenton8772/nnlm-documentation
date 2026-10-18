# Security and Trust

This document covers how NNLM supports operator trust: what it exposes for audit, how it handles credentials and access, and where its guarantees stop.

---

## Trust Comes From Inspectability

For every answer, NNLM can show:

* the question as received and as resolved (after pronoun resolution)
* the query plan
* every retrieved record, with the NAM paths that reached it
* every selector score and keep decision, and any guard that fired
* which records were cited
* how long each stage took

One-shot API responses also list markers that named no shown record (`invalid_citations`) and say whether the decode ran on CPU or GPU.

An operator can therefore trace a claim to a record, the record to the retrieval path that found it, and the path to the plan that requested it.

---

## Knowledge Boundary

Facts are meant to come from records in NAM, not from model weights:

* the generator only sees records selection kept;
* it does not run when there is no evidence;
* markers naming anything it was not shown are not counted as citations;
* stored past answers are never treated as evidence.

This narrows what a trained model can contribute from its own weights. It does not eliminate it: a generator can still produce a phrase not supported by its input. Answers drawn from parametric memory are measured on every candidate model, and a model that produces them does not ship.

---

## Session Isolation

Conversation turns are stored in NAM and can be returned by retrieval. Turns from other sessions are filtered out of the evidence for the current session, and when the current session cannot be established, every session record is treated as foreign. NNLM's own bookkeeping documents (such as the session index) are always excluded.

The session index is a single shared document listing sessions for the UI sidebar. Deployments that serve multiple users should account for that: session listing is not per-user.

---

## Access Control

* **NNLM to NAM.** The encoder authenticates to NAM's query service with a JWT obtained from NAM's login endpoint; session turns are written through NAM's authenticating KV path with that token. The Helm chart refuses to render with a missing or placeholder NAM credential, and prefers it from a Kubernetes secret.
* **Callers to NNLM.** The encoder supports an opt-in shared secret: when set, every route except `/health` requires a matching `X-NNLM-Token` header. `/ask` is the intended external entry point; the decoder need not be exposed outside the cluster.
* **The UI.** The web UI has no login of its own and calls the encoder with the shared secret, so it is meant to be exposed only to trusted users or behind an access layer of the deployment's choosing.
* **GPU provisioning.** NNLM holds only a narrow token for the GPU registry. Cloud provider credentials stay with the registry and never reach NNLM. GPUs are provisioned only on an explicit prewarm and released on request or after inactivity.

---

## Credentials

Credentials are supplied through Kubernetes secrets or deploy-time values, not baked into images. GPU registry tokens are created out of band and not templated by the chart, so an upgrade cannot rotate them unexpectedly.

---

## Failure Is Visible

* A rejected session write returns `ok: false` from the encoder and is logged, never reported as stored.
* Markers that name no shown record are logged by the decoder and counted in one-shot responses.
* On one-shot requests, a GPU fallback to CPU shows in the response's execution field.
* The UI shows each service's health and the GPU's state; a stage skipped by a gate is shown as skipped, not failed.

---

## Where Guarantees Stop

* NNLM does not verify that NAM's records are true.
* NNLM does not guarantee every generated sentence is supported by its citation.
* Retrieval reflects NAM's current state, which changes as data (including session turns) is ingested, so the same question can retrieve different records over time.

-> See also: [Design Principles](../PHILOSOPHY/DESIGN_PRINCIPLES.md) | [Grounding Model](../ARCHITECTURE/GROUNDING_MODEL.md) | [Contributions](CONTRIBUTIONS.md)
