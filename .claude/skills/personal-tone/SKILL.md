---
name: personal-tone
description: Third link in the message-response chain. Receives a raw draft plus the original message from /draft-writer, mines 10-15 of Sashank's actually-sent messages from connected tools (Outlook Sent Items, Teams history), analyzes his writing patterns, and rewrites the draft to match them exactly without changing meaning. Hands the result to /qa-check. Trigger with "/personal-tone", when draft-writer passes a draft, or when the user asks to make any text "sound like me".
---

# Personal Tone

Third link in the chain: message-triage → draft-writer → **personal-tone** → qa-check.

## Input

Expect: the raw draft, the full original message, sender name, and channel. If the draft is missing, stop and ask.

## Step 1 — Collect voice samples

Pull 10–15 messages Sashank actually sent, from whatever is connected:

- **Outlook**: `outlook_email_search` with `folderName: "Sent Items"` — prefer replies to real people over calendar responses and forwards.
- **Teams**: `chat_message_search` for messages he authored, if available.
- Prefer samples matching the current channel and a similar recipient relationship (a reply to Gita for a Gita reply; peer messages for cohort mail).

Fallback: if fewer than 10 samples are retrievable, use what exists plus the curated voice sample and pattern in CLAUDE.md §3 (the Jul 6 sick-day note), and say in the output how many real samples informed the rewrite. Never fabricate a style from zero samples silently (never-do rule 4).

## Step 2 — Analyze the patterns

Across the samples, extract:

- **Sentence length** — average and range; where he writes fragments vs. full sentences.
- **Vocabulary level** — plain vs. formal word choices; contractions or not; technical terms he uses casually.
- **Punctuation habits** — exclamation points (per CLAUDE.md §3: one warm opener may take one; nowhere else), em-dashes, parentheticals (he uses one for context), ellipses, emoji.
- **Openings** — "Hi \<first name\>," plus one warm line, per the known pattern; verify against samples.
- **Closings** — "Best, Sashank" on email; check what he does on Teams (likely nothing).
- **Structural tics** — proactive offers ("Is there anything I should…"), thanks before sign-off, paragraph length.

## Step 3 — Rewrite

Rewrite the raw draft to match the observed patterns **exactly**, under two hard constraints:

1. **Meaning is frozen.** Every fact, answer, commitment, and date in the raw draft survives unchanged. Nothing new is added.
2. **Channel-appropriate.** Email gets the full opener/closer pattern; Teams gets his chat register; a text gets brevity.

## Handoff

After rewriting, immediately invoke the `/qa-check` skill, passing:

1. the rewritten message,
2. the full original message (for reference),
3. the sample count used for the voice analysis.
