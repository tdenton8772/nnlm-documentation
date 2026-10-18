# Generation Model

The generation stage writes the answer. It receives the question and the records selection kept, and returns a short answer with numbered citations.

---

## The Model

The generator is a fine-tuned **T5 encoder-decoder** model, trained for grounded, cited answering.

Why an encoder-decoder rather than a large decoder-only chat model:

* **Conditioned on the records.** The encoder reads the question and the selected records as one input; the decoder writes the answer from that encoding. The model is trained to answer from what it is given rather than from parametric memory.
* **Small enough for modest hardware.** It runs on CPU from int8-quantized weights. A GPU speeds it up but is optional.
* **Citations are structural.** Records are numbered in the input and the model emits `[N]` markers that can be checked against that numbering.
* **Declining is part of its output.** When the records it is shown do not contain the answer, it can say so instead of answering.

---

## What the Generator Sees

The input is plain text, built from the selection result:

```
query: <question>
[1] <text of first kept record, truncated>
[2] <text of second kept record, truncated>
...
```

* Only **kept** records are shown. Records selection dropped do not appear at all, so `[N]` can only refer to something the generator was given.
* Each record is truncated to a fixed length.
* The exact input layout is part of each model's contract and is set per deployment to match what that model was trained on. Serving a model a layout it was not trained on was measured to cost accuracy.

Decoding is greedy (no sampling, single beam), with a cap on answer length.

---

## Citation Check

After generation, every `[N]` in the answer is checked:

* a marker that refers to a shown record is kept as a citation, and mapped back to that record's position in the full selection panel so the UI flags the right record as *cited*;
* a marker that refers to nothing shown is left out of the citation list, so it links to no record. The decoder logs it, and the one-shot API reports it as `invalid_citations`.

A nonzero invalid-citation count is a signal that the generator is drifting from its input.

---

## When Generation Does Not Run

Generation is skipped, and the answer comes from the selection stage, when:

* the retrieve stage's quality verdict is insufficient;
* there are no records with text;
* selection kept nothing.

In each case the reply is a decline, not a guess.

---

## Where It Runs

The decoder runs on CPU by default.

An operator can **prewarm** a GPU from the UI (or `POST /v1/gpu/prewarm` on the decoder). The decoder asks an external GPU registry for a GPU running the GPU serving image; once that GPU reports it is serving, the decoder sends its sentence-encoder and generator calls there. For one-shot requests (`/ask`), the whole select-and-generate step runs on the GPU host. Without a prewarm, nothing is provisioned and everything runs locally. The planners run on the encoder's CPU either way.

The decoder holds the prewarmed GPU through the registry while it is in use. It is released when the operator releases it or after a period of inactivity. Whenever no GPU is serving, including while one boots, and whenever the GPU is unreachable, calls run on the local CPU model. One-shot responses report where the decode ran (`execution: gpu | local`).

Both paths use the same models and decoding settings, but different numeric precision (the CPU generator is int8-quantized; the GPU runs it at full precision), so wording can occasionally differ between them.

The decoder image and the GPU serving image are built as a pair. The decoder refuses to prewarm a GPU image it was not paired with, so the GPU and CPU paths serve the same models.

---

## Training

The generator and the selector are trained offline and frozen in the images that serve them. A new generator replaces the deployed one only if it beats it on a fixed, graded question set; see [Current State](../ROADMAP/CURRENT_STATE.md#evaluation).

---

## What Generation Does Not Do

* It does not retrieve or choose evidence; those are earlier stages.
* It does not learn at query time.
* It does not use external hosted language models in the primary path.

If the trained models are not loaded, the decoder has a fallback ladder: a hosted OpenAI-compatible model *if* an API key is configured, then a rule-based extractive reply that quotes the top records with citations. A runtime error in the trained path goes straight to the rule-based reply. The reference deployment configures no hosted-model key.

-> See also: [Record Selection](RECORD_SELECTION.md) | [Grounding Model](GROUNDING_MODEL.md) | [Pipeline Model](PIPELINE_MODEL.md)
