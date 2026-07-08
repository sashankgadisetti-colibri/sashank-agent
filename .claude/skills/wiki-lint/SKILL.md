---
name: wiki-lint
description: Read-only health check of the wiki/ folder. Scans every file for duplicate entries across pages, stale or missing "Last updated" fields (missing or older than 30 days), missing required sections, and topic files absent from wiki/index.md. Reports findings; changes nothing. Trigger with "/wiki-lint", "lint the wiki", "check the wiki", or "is the wiki healthy".
---

# Wiki Lint

Read-only scan of `wiki/`. Format reference: `wiki/index.md`. **This skill never modifies any file** — it reports.

## Canonical page format (shared with wiki-ingest and wiki-query — must match exactly)

```markdown
# <Topic Title>

<one-line summary>

## What to read this for

<when this page answers your question>

## <Subtopic heading>

<body content, organized by subtopic — one ## section per subtopic>

**Last updated:** YYYY-MM-DD
```

## Checks

Scan every `.md` file in `wiki/` (index.md included, as catalog):

1. **Duplicate entries across files** — the same fact, definition, or process documented in two or more topic files. Quote both locations.
2. **Stale or missing dates** — any page whose `**Last updated:**` field is absent, malformed, or more than 30 days before today.
3. **Missing required sections** — a page lacking any of: the one-line summary directly under the title, the `## What to read this for` section, or the `**Last updated:**` field.
4. **Orphan files** — any topic file in `wiki/` not listed in `wiki/index.md`'s catalog; also the reverse (catalog rows pointing at files that don't exist).

## Report format

One section per check, findings as a list: file, line/quote where useful, and what rule it violates. Placeholder content (angle-bracket templates) counts as "structure present, content empty" — report it as its own category, not as a missing section. End with a one-line verdict: file count, finding count, and whether the wiki is query-safe.

**Make no changes.** Fixes are proposed to Sashank and belong to wiki-ingest (with diff approval) or manual edits — never to lint.
