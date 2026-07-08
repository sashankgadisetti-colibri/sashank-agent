---
name: message-triage
description: Entry point of the message-response chain. Read incoming messages from whatever messaging tools are connected in this session (Outlook email, Teams chat, Twilio texts — adapt to what's actually available), rank each by urgency (High / Medium / Low / No Reply Needed), and hand every High or Medium message to /draft-writer. Trigger with "/message-triage", "triage my messages", "handle my inbox end to end", "run the message chain", or "process my incoming messages".
---

# Message Triage

First link in the four-skill chain: **message-triage → draft-writer → personal-tone → qa-check**.

## Steps

1. **Discover connected inboxes.** Check the available tool list for messaging sources. In this environment expect: Outlook email (`outlook_email_search`), Teams chat (`chat_message_search`), Twilio calls/texts (`twilio_search`). Use every one that is present; skip gracefully anything that isn't. Never invent a source.

2. **Pull incoming messages.** Default window: since the last triage run if a previous report exists, otherwise the last 24 hours. Fetch full message bodies (`read_resource`) for anything that might need a reply — rankings based on subject lines alone are guesses, and guesses get labeled.

3. **Rank each message** using the triage value function in CLAUDE.md §3:
   - **High** — from or affecting Gita; touching a demo or program deliverable; someone blocked on Sashank; explicit deadline inside one business day.
   - **Medium** — needs a reply from Sashank but none of the above; commitments forming; questions only he can answer.
   - **Low** — FYI threads, org-wide announcements he may want to skim.
   - **No Reply Needed** — newsletters, automated notices, threads where someone else owns the reply.
   - Personal messages (family, friends) are off the books entirely — do not list, rank, or draft for them (CLAUDE.md §2).

4. **Output the triage table**: sender, subject/first line, channel, rank, one-line reason. Counts only for Low and No Reply Needed.

5. **Hand off.** For each **High or Medium** message, immediately invoke the `/draft-writer` skill, passing:
   - the full original message text (verbatim, not summarized),
   - sender name, role/relationship if known (check CLAUDE.md §3 People and `memory/` if present),
   - the thread's prior context if the message is a reply,
   - the urgency rank and the one-line reason.
   Process High messages first, then Medium.

6. **Close the loop.** After all handoffs complete (each ends with a qa-check status), append a run summary: messages scanned, ranked counts, drafts produced, qa statuses. All drafts are held for Sashank's approval — this chain never sends anything (CLAUDE.md never-do rule 2).
