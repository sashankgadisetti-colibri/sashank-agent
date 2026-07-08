---
name: debate
description: >
  Pressure-test a real decision by spawning two independent subagents —
  an Advocate arguing the strongest case for, a Skeptic arguing the
  strongest case against — then synthesizing a locked recommendation.
  Trigger with "/debate <decision>", e.g. "/debate should we migrate to
  microservices" or "/debate should I take this offer".
---

# Debate

Spawns two temporary, independent subagents against the same decision brief,
then forces a synthesis that locks a call. Broad-purpose — works on any
decision, not scoped to a particular role.

## Step 1 — Frame the brief

Take the user's decision statement and turn it into a short, neutral brief:
the decision being weighed, the options on the table, and any constraints
the user has already stated (budget, timeline, must-haves). Do not editorialize
in the brief — both personas get the identical, neutral framing.

## Step 2 — Spawn both personas in parallel

Use the Agent tool to launch **two separate subagents simultaneously** (not
one agent role-playing both sides). Each receives the identical decision brief.

**Advocate** — argues the strongest evidence-based case FOR the position.
Steelman it: find the best data, precedent, and expert consensus supporting
the move. Evidence over enthusiasm — no cheerleading, no assumed benefits
without support. End with the three strongest points in favor, ranked.

**Skeptic** — argues the strongest case AGAINST. Find the weak assumptions
buried in the proposal, the hidden costs nobody has priced in, and the risks
that show up in comparable decisions elsewhere. Evidence over contrarianism —
concede if the case is genuinely solid rather than manufacturing objections.
End with the three strongest objections, ranked.

Both run concurrently; wait for both to complete before synthesis.

## Step 3 — Synthesize and lock a decision

Once both return, produce a synthesis with:

1. **Where they clash** — the specific points where Advocate and Skeptic
   directly contradict each other, named explicitly.
2. **What each got right** — anything from either side that survives
   scrutiny regardless of which way the decision goes.
3. **The locked call** — state the recommended decision plainly. Never punt,
   never say "it depends" as the final word — if the evidence is genuinely
   split, say so, then still name which way you'd lean and why.
4. **The audit trail** — output the full Advocate argument, the full Skeptic
   argument, and the synthesis reasoning, in that order, so the user can see
   exactly how the call was reached, not just the conclusion.

## Constraints

- Never skip straight to a synthesis without running both personas.
- Never let one persona see the other's argument before both complete —
  independence is the point.
- The decision is the user's regardless of the lock — present it as a strong
  recommendation, not an executed action.
