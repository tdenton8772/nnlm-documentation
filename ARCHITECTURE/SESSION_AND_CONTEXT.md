# Session and Context

NNLM supports multi-turn conversation. It does this by **storing each turn in NAM and resolving references against those stored turns**, not by pasting the whole conversation into a model's input.

---

## Sessions

Each conversation has a session id. The UI keeps it in the browser so a reload or reconnect returns to the same conversation, and the sidebar lists previous sessions so any of them can be reopened.

---

## Where Turns Live

After each answer, the UI asks the encoder to write the turn into NAM's main bucket, under a key made from the session id and the turn number. A turn stores:

* the question and the answer
* entity names — the answer's people, organisations and places first, then the question's entities
* short excerpts from the top records

The write goes to the data node that owns the key. If the store rejects it, `/write_turn` returns `ok: false` and logs the failure instead of reporting success. The UI does not wait for the write.

The one-shot `/ask` endpoint reads a session's stored turns when it is given a session id, but does not write a new one.

Because turns are ordinary NAM documents, NAM ingests and addresses them like any other record. A session index document records each session's title, turn count and last update for the sidebar.

---

## Reading the Session Back

At the start of each question, the plan stage reads the session's turns from NAM by key, in order. Their questions become the planner's history, and their entities are added to the extracted entities.

When the UI reconnects to an existing session, it loads the same stored turns to redraw the conversation.

---

## Follow-Up Pronouns

If the new question contains a pronoun, the plan stage replaces one of them with a referent taken from the stored turns, before any retrieval:

1. Walk the turns newest first.
2. Skip turns whose question itself contains a pronoun ("Who did he defeat?"); they introduce no new subject.
3. Normally, the referent is the **subject of that previous question** (its longest capitalised name).
4. Exception: after a question that asks for someone *other* than the person it names — "Who killed X?" — the referent is a **name from that turn's answer**.
5. Possessive pronouns become `X's`.

Example:

* "Who was Abraham Lincoln?" -> answered.
* "Where was he born?" -> resolved to "Where was Abraham Lincoln born?"
* "Who killed Abraham Lincoln?" -> answered: John Wilkes Booth.
* "Where did he die?" -> resolved to "Where did John Wilkes Booth die?"

Both the resolved and original questions are sent to NAM, and the UI shows the resolved form in the plan.

---

## Session Records as Evidence

Turns are in NAM, so a NAM query can return them. Rules applied before selection:

* turns from **other** sessions are never evidence;
* stored question-answer text is never evidence, including the current session's — it is the system's own past output;
* if selection keeps only echoes of earlier answers while source records exist, the best source record is added back.

The session's role is to resolve *what is being asked*, not to supply facts.

---

## What Sessions Do Not Do

* They do not change any model. Nothing is learned during a conversation.
* They do not make an answer more trustworthy: facts still have to come from source records.
* Resolution is rule-based and replaces one pronoun per question. Ambiguous references (two candidate people, a pronoun meaning a place) can resolve wrongly; the resolved question is shown so this is visible.
* At most the first twenty turns of a session are read back.

-> See also: [Pipeline Model](PIPELINE_MODEL.md) | [Grounding Model](GROUNDING_MODEL.md) | [What NNLM Can Do](../CAPABILITIES/WHAT_NNLM_CAN_DO.md)
