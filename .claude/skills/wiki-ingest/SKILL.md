---
name: wiki-ingest
description: Convert a raw source (pasted document, meeting transcript, article, email summary, or live tool data) into a structured wiki page in wiki/, matching the canonical page format, and update wiki/index.md. Trigger with "/wiki-ingest", "add this to the wiki", "turn this into a wiki page", or "ingest this into the knowledge base".
---

# Wiki Ingest

Converts raw source material into a structured page in `wiki/`. Format reference: `wiki/index.md`.

## Canonical page format (shared with wiki-lint and wiki-query — must match exactly)

```markdown
# <Topic Title>

<one-line summary>

## What to read this for

<when this page answers your question>

## <Subtopic heading>

<body content, organized by subtopic — one ## section per subtopic>

**Last updated:** YYYY-MM-DD
```

## Behavior

**Ask two questions before doing anything else:**

1. "What's the target topic / file name for this wiki page?"
2. "What's the source?" — options: **Paste text**, **Outlook email**, **Teams**, **SharePoint**, **GitHub**, or **Mixed** (multiple sources). *(This session has no Gmail, Slack, Notion, or Google Drive connectors — offer only sources that are actually connected, and adapt this list if connectors change.)*

Then:

3. **Read the source.** Pasted text is used verbatim; connected tools are searched and read via their MCP tools. Fetch full content, not summaries.
4. **Distill only durable facts** — decisions, processes, names, definitions, dates that will still be true next month. Drop pleasantries, one-time logistics, and anything speculative. A guess that must be kept gets labeled as a guess (CLAUDE.md never-do rule 4).
5. **Create or update the page** in the canonical format. Body sections are organized by subtopic. Set **Last updated** to today.
6. **Update `wiki/index.md`** — add or refresh the topic's catalog row (file, summary, last updated).

## Guardrails (from CLAUDE.md Wiki Maintenance — these override the steps above)

- Before writing, **propose the change as a diff**: show exactly what would be added or changed in the topic file and in index.md, and wait for approval.
- **Never silently overwrite** an existing wiki file — updates are shown as diffs against current content, never as wholesale replacement.
