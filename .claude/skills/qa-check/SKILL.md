---
name: qa-check
description: Final link in the message-response chain. Receives a rewritten draft plus the original message from /personal-tone and reviews it for grammar errors, ambiguity, missing information, and anything that could read as rude or passive-aggressive. Outputs STATUS Ready to send, or STATUS Revised with numbered issues and a corrected version. Trigger with "/qa-check", when personal-tone passes a rewrite, or when the user asks to check any outgoing message before sending.
---

# QA Check

Final link in the chain: message-triage → draft-writer → personal-tone → **qa-check**.

## Input

Expect: the rewritten draft and the full original message. If the original is missing, stop and ask — completeness cannot be judged without knowing what was asked.

## Review dimensions

Check the draft against all four, in order:

1. **Grammar and mechanics** — spelling, agreement, tense, broken sentences, name misspellings (recipient names especially — a misspelled "Gita" costs more than any other typo).
2. **Ambiguous phrasing** — any sentence a reasonable reader could take two ways; any "it/this/that" with an unclear antecedent; any date without a day ("Friday" → "Friday, Jul 10"); any time without a timezone when the recipient is in Central and Sashank is in Pacific (CLAUDE.md §3).
3. **Missing information** — anything the recipient would need to act without a follow-up round-trip: answers promised by the original message's questions, links or attachments referenced but absent, next steps without owners or dates.
4. **Tone hazards** — anything that could read as rude, dismissive, sarcastic, or passive-aggressive; hedged commitments that read as reluctance; terseness that reads as coldness to a senior recipient. Judge as the *recipient* would, not as the writer intended.

## Output — exactly one of two statuses

**If every dimension passes:**

```
STATUS: Ready to send
```

followed by the message, byte-for-byte unchanged.

**If anything fails:**

```
STATUS: Revised
```

followed by a numbered list of every issue found (dimension, quoted phrase, why it fails), then the fully corrected version. Corrections fix the listed issues only — the voice from personal-tone and the meaning from draft-writer are otherwise preserved.

## End of chain

Either status ends the chain with a **held draft**. Per CLAUDE.md never-do rule 2, nothing is ever sent — "Ready to send" means ready for Sashank's approval, and the final report to him presents each draft with its status and channel. If this run was invoked from /message-triage, return the status and final text to that run's summary.
