---
name: clarify
description: Interview the user one question at a time to resolve open decisions in a plan or design until shared understanding is reached. Activate ONLY when the user explicitly asks to be interviewed, grilled, stress-tested, or clarified about a plan — phrases like "clarify", "grill me", "stress-test this", "interview me about this design", or "/clarify". Do NOT activate from inferred ambiguity, from a context-compaction summary, or from general planning discussion — wait for an explicit invocation in the most recent user message. Once active, the skill stays active for the rest of the interview; subsequent answer-question turns do not need re-triggering.
---

# Clarify

Interview the user one question at a time to resolve open decisions about a plan, design, or task until both sides hold the same mental model. Walk the decision tree by dependency: roots before leaves, depth-first within a branch.

## Ground first

Before the first question:

- Read the relevant code, docs, links, or external references. **If a question can be answered by reading, read instead of asking.**
- Map the decision space — major branches and what depends on what.
- If the user has not stated a plan, ask them to state it (or sketch one for confirmation) before drilling.

## Question shape

Each turn, ask exactly one question in this format:

```
## <Short question label>

<Context overview — 1–4 sentences. Where in the design we are,
what's already decided, what this branch depends on. If the
question concerns topology, flow, or relationships, include an
ASCII diagram in this block.>

**Question:** <The one concrete question.>

**Recommended answer:** <Your default, one sentence.>
**Why:** <Reasoning, 1–2 sentences.>
```

Rules:

- **One question per turn.** Wait for the answer before asking the next.
- **Always lead with the context overview.** The user should not need to rebuild the surrounding state to answer.
- **Always offer a recommended answer.** Never ask without taking a position — making the user choose blind is lazy.
- **ASCII diagram when relationships matter.** Two boxes and an arrow beat a paragraph for "does the worker write to the queue directly, or via the API?"
- **Terse prose.** No throat-clearing. No restating what was just decided.

### Worked example

If the user wants to add a webhook on order ship:

```
## Webhook delivery: inline or queued?

`OrderService.markShipped()` runs synchronously today and already
makes one external call (customer email):

  markShipped()
    ├─ updates DB
    └─ sends email (sync, ~200ms p95)

Adding webhooks inline means N more HTTP calls per shipment, all on
the request path. Receivers can be slow or down.

**Question:** Inline in markShipped(), or enqueued (Sidekiq) and
delivered async?

**Recommended answer:** Enqueue.
**Why:** Sidekiq is already used for the shipping label PDF — same
shape, same retry semantics. Inline coupling makes ship latency
depend on third parties.
```

## Choosing the next question

1. Resolve roots first — questions whose answers constrain other questions.
2. Depth-first within a branch until you hit a leaf, then back up.
3. Skip questions whose answers became implied by earlier answers.
4. If the user changes a previous answer, recheck what it invalidates.

## Stop when

- All branches with material consequences are resolved.
- The user signals they have enough to proceed.
- Remaining questions are implementation details the user can resolve while writing code.

End with a short recap: resolved decisions and any deliberately-deferred ones. No filler.

## Anti-patterns

- Asking without a context overview.
- Asking without a recommended answer.
- Asking multiple questions in one turn.
- Asking what the codebase can answer.
- Prose where an ASCII diagram would be faster.
- Continuing past useful resolution.
