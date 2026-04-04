# Security and Trust

NNLM is designed to be **trustworthy by construction**, not by policy alone.

This document explains how NNLM approaches security, auditability, and operational trust — and why those properties are inseparable from its architectural choices.

---

## Trust as a System Property

In NNLM, trust is not layered on afterward.

It emerges from:

* grounding to retrieved evidence
* deterministic retrieval and selection
* citation as structural output
* rejection over hallucination
* per-stage observability

The system is designed so that operators can reason about *what the system said*, *why it said it*, and *what evidence supports it*.

---

## No Training Data in Responses

NNLM does not generate from its training weights.

Every factual claim in a response is grounded to a record retrieved from NAM.

This means:

* responses do not contain memorized training data
* sensitive information from training corpora does not leak into outputs
* the knowledge boundary is defined by NAM's contents, not the model's weights

This is a structural guarantee, not a prompting convention.

---

## Deterministic Behavior Enables Auditing

The retrieval and selection stages of NNLM are deterministic.

Given the same query and the same NAM state:

* the same records are retrieved
* the same selection decisions are made
* the same evidence base reaches the generation model

This enables:

* reproducibility of responses
* forensic analysis of past outputs
* regression testing across system updates
* compliance verification in regulated environments

If a response cannot be reproduced, something has changed — and the change can be identified by comparing stage outputs.

---

## Credentials and Secrets Management

NNLM separates credentials from application code and configuration.

* API keys, authentication tokens, and service credentials are managed via secrets infrastructure
* Credentials are never embedded in configuration files, container images, or source code
* Deployment configurations reference external secrets rather than storing them inline

This supports integration with external secret management systems and follows standard security practices.

---

## Network Isolation in Deployment

NNLM's deployment architecture supports network-level isolation.

Internal components — the encoder, NAM, the selection model, the generation model — communicate within an isolated network.

External access is:

* mediated through defined entry points
* authenticated where appropriate
* logged for audit purposes

Internal services are not exposed to external networks.

---

## Pipeline Isolation

Each stage of the NNLM pipeline has explicit boundaries.

* the encoder does not access the generation model
* the selection model does not access NAM directly
* the generation model does not access NAM directly
* each stage operates on the output of the previous stage, nothing more

This isolation:

* limits blast radius when a stage misbehaves
* prevents unauthorized data flow between stages
* makes the system's behavior analyzable at each boundary

---

## Citation as an Audit Trail

Every response includes citation markers linking claims to specific retrieved records.

This creates a natural audit trail:

* any claim can be traced to its evidence
* any evidence can be traced to its NAM address
* any NAM address can be traced to its retrieval path
* the query plan that initiated retrieval is preserved

This traceability exists at the architectural level. It does not require additional tooling or post-hoc analysis.

---

## Rejection as a Safety Mechanism

When NNLM cannot ground a response in sufficient evidence, it rejects the query.

This is a safety mechanism:

* it prevents hallucinated responses from reaching users
* it signals knowledge gaps explicitly
* it prefers honesty over helpfulness

In safety-critical environments, a system that says "I don't know" is more trustworthy than a system that always has an answer.

---

## What NNLM Does Not Rely On

NNLM does not rely on:

* prompt hardening to prevent hallucination
* output filters to catch wrong answers
* model alignment techniques to ensure honesty
* post-processing to add citations

Instead, it relies on:

* pipeline architecture that makes hallucination structurally difficult
* record selection that removes noise before generation
* citation markers as structural output of the generation model
* rejection as a first-class response

Security emerges from structure, not from rules.

---

## Failure Modes Are Visible

NNLM is designed so that failure is observable.

When the system produces a wrong answer:

* citation markers identify which record was cited
* selection scores show why that record was kept
* retrieval metadata shows why that record was returned
* the query plan shows how the query was interpreted

When the system rejects a query:

* selection scores show that no records passed the threshold
* retrieval metadata shows what was found (and why it was insufficient)

Failures are diagnosable because the pipeline is transparent.

---

## Summary

NNLM is designed to be trusted because it is:

* grounded to retrieved evidence
* deterministic in retrieval and selection
* transparent via citations and per-stage observability
* honest via rejection when evidence is insufficient
* isolated at the network and pipeline level
* auditable from query to claim to evidence to address

These properties are intentional, not incidental.

Trust in NNLM comes from understanding how it works — and knowing that every claim it makes can be traced to its source.

-> See also: [Design Principles](../PHILOSOPHY/DESIGN_PRINCIPLES.md) | [Grounding Model](../ARCHITECTURE/GROUNDING_MODEL.md) | [Contributions](CONTRIBUTIONS.md)
