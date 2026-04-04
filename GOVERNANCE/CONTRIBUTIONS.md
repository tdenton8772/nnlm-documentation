# Contributions and Collaboration

NNLM is a **collaborative system**, but it is not an open-contribution project.

Contributions are welcome **by alignment and agreement**, not by default pull request.

This document defines how collaboration works, what kinds of contributions are appropriate, and how architectural integrity is preserved.

---

## Guiding Principle

NNLM prioritizes **coherence over velocity**.

The system is built around non-negotiable invariants:

* grounding over fluency
* citation over assertion
* rejection over hallucination
* structure over prompting
* determinism over creativity

Any contribution that weakens these principles is not a contribution — even if it adds features.

---

## How to Engage

If you are interested in contributing to NNLM:

1. Read the documentation — especially [Design Principles](../PHILOSOPHY/DESIGN_PRINCIPLES.md) and [Terminology](../PHILOSOPHY/TERMINOLOGY.md)
2. Understand the architectural invariants
3. Start a conversation about your intended contribution
4. Proceed by agreement, not assumption

Contributions are **intentional and conversational**, not transactional.

---

## What Kinds of Contributions Are Welcome

### Strongly Encouraged

* documentation improvements and clarifications
* conceptual critiques and design feedback
* evaluation scenarios and test cases
* domain-specific insights that improve coverage
* explanatory material and examples

### Conditionally Accepted

* encoder stage improvements (must preserve query plan contracts)
* selection model improvements (must preserve threshold semantics)
* generation improvements (must preserve citation behavior)
* training data contributions (under quality and scope guidance)

### Explicitly Out of Scope

* changes that remove or weaken citation requirements
* runtime learning or adaptive behavior
* ungrounded generation capabilities
* changes that break determinism in retrieval or selection
* features that bypass record selection

---

## Documentation Contributions

Documentation contributions are especially welcome.

NNLM's value depends on being understood correctly. Contributions that:

* clarify concepts
* improve examples
* fix inaccuracies
* add cross-references

are immediately valuable and have a low barrier to acceptance.

---

## Code Contributions

Code contributions require more process:

1. Discussion of intent and scope
2. Agreement on architectural alignment
3. Contribution or proposal
4. Review against pipeline invariants
5. Integration (or rejection) with clear reasoning

There is no guarantee that any code contribution will be accepted.

---

## Architectural Authority

NNLM has a **single architectural authority**.

This is not about control — it is about:

* maintaining invariants
* preventing architectural drift
* ensuring the system remains understandable

Disagreements are expected.
Fragmentation is not.

---

## Intellectual Property

All contributions:

* fall under the existing licensing model
* do not grant ownership or usage rights by default
* may be incorporated, modified, or declined

If IP terms matter for your contribution, discuss them *before* contributing.

---

## Why This Structure Exists

NNLM is designed to solve a specific problem — grounded, cited language generation from deterministic memory.

That requires:

* thoughtful iteration
* shared vocabulary
* mutual trust
* long-term coherence

This contribution model exists to protect that work — not to exclude people.

---

## Questions or Proposals

If you are interested in contributing:

* ideas
* code
* documentation
* research
* evaluation data

Start with a conversation.

Alignment comes before contribution.

-> See also: [Licensing Model](LICENSING_MODEL.md) | [Design Principles](../PHILOSOPHY/DESIGN_PRINCIPLES.md) | [Security and Trust](SECURITY_AND_TRUST.md)
