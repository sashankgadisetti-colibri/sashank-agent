---
name: wiki-query
description: Route a question to the right wiki pages without answering it. Takes a question, scans wiki/index.md, and returns the 1-3 topic files most likely to contain the answer, each with a one-sentence reason. Says plainly when nothing in the wiki is relevant. Trigger with "/wiki-query", "where in the wiki is...", "which wiki page covers...", or as the lookup step before answering any domain-specific question (CLAUDE.md Wiki Maintenance rule 1).
---

# Wiki Query

Surfaces *where to look*, never the answer itself. Format reference: `wiki/index.md`.

## Canonical page format (shared with wiki-ingest and wiki-lint — must match exactly)

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

Input: a question.

1. **Read `wiki/index.md`** — the catalog's file names, summaries, and dates are the primary routing signal.
2. **If the catalog alone can't discriminate**, open candidate files and read only their one-line summary and `## What to read this for` sections — that's what those sections exist for.
3. **Return 1–3 topic files**, ranked, each as: file name + one sentence on why it's likely relevant. Include the page's Last updated date so the reader can judge staleness.
4. **Do not answer the question.** No excerpts beyond what's needed to justify relevance, no synthesis, no advice. The deliverable is a pointer, not an answer.
5. **If no topic file is clearly relevant, say so** in one line — and name it as a wiki gap worth an ingest, without inventing a match.
