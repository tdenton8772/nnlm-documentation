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
* declining over guessing
* structure over prompting
* inspectable stages
* measurement over assumption

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

* planning and retrieval improvements (must preserve the plan and retrieve stage contracts)
* selector improvements (must keep the keep-mask contract and the grounding guards)
* generator improvements (must keep checked `[N]` citations and declining; a new model ships only if it beats the deployed one on the graded question set)
* training data contributions (must carry provenance and licensing terms)

### Explicitly Out of Scope

* changes that remove or weaken citation checking
* runtime learning or adaptive behavior
* generation without selected evidence, or bypassing the generation gates
* treating stored past answers as evidence
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

NNLM is designed to solve a specific problem — grounded, cited answers from records in NAM.

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
