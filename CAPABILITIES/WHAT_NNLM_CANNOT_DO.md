# What NNLM Cannot Do

These are things NNLM does not do by design. They are not maturity gaps; those are in [Current Limitations](CURRENT_LIMITATIONS.md).

If a use case fundamentally needs one of these, NNLM is the wrong tool.

---

## 1. Answer From General Knowledge

NNLM's knowledge is whatever is in its NAM instance. It does not search the web and does not treat its models' training data as a knowledge source. A question about something NAM does not hold should be declined.

---

## 2. Creative or Open-Ended Writing

No fiction, brainstorming, or "imagine if". The generator is trained to write short answers from supplied records.

---

## 3. Long-Form Output

Answers are short, a few sentences, by design and by model size. NNLM is not a report writer or a summariser of arbitrary documents.

---

## 4. General Conversation

NNLM does not do small talk, opinions or role-play. Its conversational ability is limited to follow-up questions about the same subjects.

---

## 5. Learn During Use

No model changes at query time. Conversation turns are stored in NAM, but that is memory of what was asked, not learning, and stored answers are never used as evidence for later answers.

---

## 6. Act on External Systems

NNLM produces text. It does not call tools, run workflows or take actions.

---

## 7. Guarantee Correctness

NNLM can show where a claim came from. It cannot make a wrong source right, and as a trained generator it can still occasionally misstate what a cited record says. Its guarantee is traceability, not truth.

---

## Summary

NNLM is a question-answering interface to the records in NAM. Outside that shape it will feel rigid, because it is.

-> See also: [What NNLM Can Do](WHAT_NNLM_CAN_DO.md) | [Current Limitations](CURRENT_LIMITATIONS.md) | [Design Principles](../PHILOSOPHY/DESIGN_PRINCIPLES.md)
