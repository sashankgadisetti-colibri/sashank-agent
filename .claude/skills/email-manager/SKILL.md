---
name: email-manager
description: Triage inbox, surface what needs attention, and draft replies in your voice. Use this skill whenever the user says "check my inbox", "triage my emails", "what do I need to reply to", "draft replies for my unread emails", "what emails need my attention", "catch me up on email", "what did I miss", or any variation of wanting to process, prioritize, or respond to email. Also trigger when the user says "handle my inbox", "go through my emails", "what's in my inbox", or pastes an email and asks what to do with it. Always use this skill rather than manually searching Outlook when the goal is inbox triage or drafting replies. If the user mentions email and sounds like they want to take action, use this skill.
---

# Email Manager

Triage Sashank's inbox, decide what needs attention, and draft replies in his voice. Nothing gets sent — everything goes to him for review.

## Before you draft anything

Read the humanize-writing skill:
`/var/folders/2l/dykhxtgx623fnrthg17nk7_w0000gp/T/claude-hostloop-plugins/005ec62f3187a86c/skills/humanize-writing/SKILL.md`

Every draft must pass the Email Voice Guide calibration tests at the bottom of that file.

---

## Step 1: Fetch emails

Use `outlook_email_search` to pull unread emails from the last 24 hours from the Inbox. For each email that passes the triage filter below, use `read_resource` to get the full body. Skip fetching the body of emails you'll immediately discard — there's no need to read a newsletter to know it's a newsletter.

---

## Step 2: Triage

### Always discard — don't surface, don't count
- Newsletters, digests, subscription emails (Substack, industry roundups, product updates, marketing)
- Automated system notifications (build alerts, monitoring pings, deploy hooks, calendar digests)

At the end, report how many were discarded: `Discarded X emails (newsletters, notifications).`

### Flag without drafting
- Cold outreach from people Sashank has never interacted with.

Surface these with: `[FLAG — cold outreach] Subject — Sender — one-line description`

He'll decide what to do. Don't write a reply.

### Draft if there's a clear, specific contribution
- CC'd emails where Sashank has something concrete and specific to add.

If you can't identify what he'd specifically say, skip it. Don't draft a generic "thanks for looping me in" reply.

### Always draft a reply
- Direct questions addressed to Sashank
- Emails requiring a decision or approval
- Emails from his manager
- Emails from teammates or collaborators
- Emails from external clients or partners
- Anything time-sensitive (deadline named, someone waiting on him)

---

## Step 3: Classify urgency

Mark **URGENT** if any of these are true:
- Someone is blocked waiting on Sashank to proceed
- A hard deadline is named for today or tomorrow
- Sent by his manager
- Involves money, contracts, legal, payments, or access/permissions

Everything else is **STANDARD**.

---

## Step 4: Handle meeting requests

For meeting requests from people he doesn't know well, assess relevance from the content:

- Looks relevant to his work → draft an acceptance or "happy to connect" reply, note your reasoning
- Doesn't look relevant → draft a polite one-line decline

Always note: `[Assumed: accepted/declined because ...]` so he can override.

---

## Step 5: Draft replies

Apply the humanize-writing skill + Email Voice Guide to every draft. Key voice rules from his corpus:

- **Open**: brief acknowledgment, then immediately to the point
- **Sentences**: short, generous line breaks, no walls of text
- **Contractions**: always — `I'll`, `I'm`, `you'd` — even to senior people
- **Issues**: frame as "Quick question" not a complaint
- **Logistics**: confirm explicitly when committing ("The [date] works on my end")
- **Accountability**: one sentence, no groveling, move on
- **Closing**: forward-looking — "I look forward to...", "Have a great [X]!", "Best, Sashank"

For each draft, note:
- `[Assumed: ...]` — any decision you made on his behalf
- `[Need: ...]` — any information missing that would improve the reply (leave a `[PLACEHOLDER]` in the draft where it goes)

---

## Step 6: Present results

Show in this order:

---

### URGENT

```
[URGENT] #1 — Subject — Sender <email> — Date
Summary: 1-2 sentences on what this is and why it's urgent.

DRAFT:
Hi [Name],

[reply body]

Best,
Sashank

[Assumed: ...] / [Need: ...] if applicable
```

---

### STANDARD

Same format, numbered sequentially continuing from URGENT.

---

### FLAGGED

```
[FLAG — cold outreach] Subject — Sender — one-line description
```

---

### DISCARDED

```
Discarded X emails (newsletters, notifications).
```

---

Never send anything. All drafts are for Sashank's review only.
