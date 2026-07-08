---
name: draft-writer
description: Second link in the message-response chain. Receives one message plus sender context from /message-triage and writes a raw, complete draft reply — correct and thorough, deliberately unpolished, no style matching. Hands the draft to /personal-tone. Trigger with "/draft-writer" or when message-triage passes a High or Medium message; can also be invoked directly with a pasted message to answer.
---

# Draft Writer

Second link in the chain: message-triage → **draft-writer** → personal-tone → qa-check.

## Input

Expect from the caller (or the user directly): the full original message, sender name and relationship, thread context if any, and the urgency reason. If the original message text is missing, stop and ask for it — never draft a reply to a summary.

## Job

Write ONE complete draft response. Optimize for substance, not style:

- **Answer everything.** Every question in the original gets an answer; every request gets an explicit yes, no, or a concrete next step with a date.
- **Correct over graceful.** Verify facts against available sources (calendar for availability, GitHub for PR/code claims, prior threads for commitments) before asserting them. Anything unverifiable is labeled a guess or omitted (CLAUDE.md never-do rule 4).
- **Complete over brief.** Include whatever the recipient needs to act without a follow-up round-trip.
- **No style work.** Do not try to sound like Sashank. Do not add warmth, openers, or sign-offs beyond bare function. That is the next skill's job — duplicated style effort here degrades the chain.
- **Flag divergence.** If the correct response differs from the obvious one (declining, pushing back, escalating), write the draft you'd advise and note the divergence in one line above it (CLAUDE.md §2: "flag where your advice differs from the obvious response").

## Handoff

After completing the draft, immediately invoke the `/personal-tone` skill, passing:

1. the raw draft,
2. the full original message (verbatim),
3. the sender name and channel (email / Teams / text) — tone differs by channel.
