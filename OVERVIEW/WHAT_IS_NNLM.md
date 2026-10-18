# What Is NNLM?

NNLM is a language system that **answers from stored records and says which ones**.

It does not answer from what a model memorised in training. It retrieves candidate records from NAM, decides which of them are evidence, and writes a short answer from only those, marking each claim with a numbered citation. If the evidence is not there, it says the records do not contain the answer.

---

## A Simple Way to Think About It

A typical retrieval-augmented chatbot:

> Take the question. Find similar text. Paste it into a large model's prompt. Hope the model uses it.

NNLM:

> Take the question. Work out what it is asking and who it is about, using the conversation so far. Ask NAM through several retrieval paths. Score every record that comes back and keep only the evidence. Have a small generator write an answer from that evidence, citing it. Check every citation points at something the generator was shown.

---

## What Makes NNLM Different

**Evidence is selected, not dumped.**
A trained selector (a TabTransformer) scores each retrieved record using its text and NAM's metadata about it. The generator only ever sees the records the selector keeps. If nothing is kept, the generator does not run.

**The generator is small and conditioned.**
Answers are written by a fine-tuned T5 encoder-decoder model given the question and the numbered, selected records. It is small enough to run on CPU; a GPU makes it faster but is optional.

**Citations are checked.**
A marker that does not correspond to a record the generator was shown is not counted as a citation, and the decoder records it.

**Provenance is kept.**
NAM tells NNLM which of its paths (semantic address, vector similarity, graph) reached each record. That travels with the record into the UI.

**Every stage is visible.**
The UI shows the plan, what was retrieved, which records were kept and with what score, which were cited, and how long each stage took.

---

## What an Answer Looks Like

An illustrative answer:

> Abraham Lincoln was assassinated by John Wilkes Booth at Ford's Theatre in Washington, D.C. [1]. Booth was a stage actor and Confederate sympathizer [2].

Each `[N]` refers to one of the records the generator was shown, and the selection panel marks those records as cited. For every retrieved record the panel shows its selector score, whether it was kept, whether it was cited, and badges for the NAM paths that retrieved it.

When the records do not contain the answer, the reply says so instead.

---

## What NNLM Is For

* question answering where a claim must be traceable to a source record
* knowledge bases that change, where re-training a model for every update is impractical
* deployments on modest hardware, including no GPU at all
* settings where "the records do not say" is better than a guess

It is not designed for creative writing, open-ended chat, autonomous action, or answering from general world knowledge.

---

## In One Sentence

**NNLM retrieves from NAM, selects the evidence with a trained model, and writes a short cited answer from only that evidence — or declines.**

-> See also: [Where NNLM Fits](WHERE_NNLM_FITS.md) | [Pipeline Model](../ARCHITECTURE/PIPELINE_MODEL.md) | [Design Principles](../PHILOSOPHY/DESIGN_PRINCIPLES.md)
