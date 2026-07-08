# RESOLVER — Skill Dispatch Rules

How Jarvis picks the right skill for each task type. CLAUDE.md §4 holds the guardrails,
channels, storage, and triggers; this file holds only dispatch. Never-do rules (CLAUDE.md §3)
override everything here — no dispatch outcome may send, spend, or skip the plan-first rule.

## The message-response chain

Four skills, fixed order, explicit handoffs baked into each SKILL.md:

```
/message-triage → /draft-writer → /personal-tone → /qa-check
```

- Full inbox-to-drafts run ("handle my inbox end to end", scheduled triage sweeps) → enter at **message-triage**.
- One pasted message needing a reply → enter at **draft-writer** (it chains the rest).
- Existing text that should sound like Sashank → enter at **personal-tone**.
- Final check on any outgoing message → enter at **qa-check** alone.
- Every chain run terminates in a HELD draft. No skill sends.

## Dispatch by task type

| Task type | Skill(s) | Notes |
|---|---|---|
| Quick inbox look — "what did I miss", "catch me up" | `email-manager` | Read-and-report; no drafting pipeline |
| Inbox processed to reply drafts | `message-triage` chain | Wins over email-manager when drafts are the deliverable |
| Daily plan (weekday mornings) | compose: `email-manager` + `search` + `task-management` | No single skill yet — assemble calendar + inbox + follow-ups |
| Follow-up / commitment tracking | `task-management` | TASKS.md + board; feeds dashboard/follow-ups.html |
| Meeting prep brief | `call-prep`, then `search` | call-prep drives; search pulls SharePoint/email context |
| Find a doc / decision / conversation | `search` | Also the tool for "where does X live" discovery |
| People, shorthand, terminology | `memory-management` | Never modifies the identity sections of CLAUDE.md |
| Work journal (Fridays) | `internal-comms` + GitHub MCP | Mine merged PRs/commits first; internal-comms formats |
| Status reports, leadership updates, FAQs | `internal-comms` | Company-format internal writing |
| Polish any outbound long-form prose | `humanize-writing` | Default for text Sashank will send or publish |
| Match Sashank's personal voice | `personal-tone` | For messages *from him*; humanize-writing is generic prose |
| Word document (.docx/.dotx) | `docx` | |
| PDF (read, merge, fill, create) | `pdf` | |
| Slide deck (.pptx/.potx) | `pptx` | |
| Spreadsheet (.xlsx/.csv) | `xlsx` | |
| Publish/search company artifacts | `prism` | Colibri Prism hub |
| Dashboard or any HTML/CSS styling | `sierra-design` | Single source of truth for look-and-feel |
| Build, edit, or eval a skill | `skill-creator` | Also owns description-tuning when dispatch misfires |

## Collision rules

1. **email-manager vs message-triage**: reading → email-manager; producing reply drafts → the chain.
2. **personal-tone vs humanize-writing**: outgoing message in Sashank's name → personal-tone; any other prose → humanize-writing.
3. **internal-comms vs docx**: internal-comms decides the content/format; docx fires only when the deliverable is a .docx file.
4. **search vs sharepoint/email tools directly**: one-source lookups may use the tool directly; anything that could live in two or more places → search.

## Fallbacks

- No skill matches → act directly with tools; do not force a skill.
- A skill's hard dependency is missing this session → say so, degrade to the nearest
  covered alternative, and label the gap.
- Wrong skill keeps firing on a phrase → fix that skill's description via skill-creator; one-line edit, log it in the work journal.

## Known gaps (no skill yet — build with skill-creator when prioritized)

Daily-plan composite, call/text return ledger, work-journal compiler, GitHub watch.
