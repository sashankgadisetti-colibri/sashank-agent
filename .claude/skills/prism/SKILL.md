---
name: prism
version: 1.1.3
description: Interact with Colibri Prism — the company's artifact and knowledge hub. Publish files, register links, search, list collections, inspect content, and manage access. Triggers on '/prism' or natural phrases like 'publish to prism', 'search prism', 'list prism collections'.
argument-hint: [publish|link|search|list|get|restrict] [args...]
allowed-tools: AskUserQuestion, Read, Bash, mcp__colibri-prism__list_scopes, mcp__colibri-prism__upsert_scope, mcp__colibri-prism__publish_artifact, mcp__colibri-prism__upload_file_base64, mcp__colibri-prism__create_upload, mcp__colibri-prism__finalize_upload, mcp__colibri-prism__add_link, mcp__colibri-prism__list_collections, mcp__colibri-prism__get_collection, mcp__colibri-prism__search_artifacts, mcp__colibri-prism__set_collection_acl, mcp__colibri-prism__delete_artifact, mcp__colibri-prism__delete_folder, mcp__colibri-prism__tag_collection, mcp__colibri-prism__untag, mcp__colibri-prism__list_tag_keys
---

# Prism

One entry point for everything in Colibri Prism. Parse `$0` (first word of argument) to route to the right action. If no argument is given, show the help menu below.

---

## Routing

| Command | Action |
|---|---|
| `publish <file>` | Upload an HTML, Markdown, or PDF file |
| `link <url>` | Register an external URL (SharePoint, Confluence, etc.) |
| `search <query>` | Search artifact names and titles |
| `list [function]` | List collections, optionally filtered by function |
| `get <path>` | Show all items in a collection |
| `restrict <path>` | Set access control on a collection |
| `tag <path> <key=value,...>` | Add/update tags on a collection |
| `untag <path> <key,...>` | Remove tag keys from a collection |
| `tags [query]` | List known tag keys, optionally filter by value pattern |
| *(no args)* | Show help |

If the argument doesn't match any command, treat the whole string as a `search` query.

**Tag routing:** If the first word of `$0` is `tag`, call `tag_collection` with `path=$1` and tags parsed from `$2` (comma-separated `key=value` pairs). If `untag`, call `untag` with `path=$1` and keys parsed from `$2` (comma-separated key names). If `tags`, call `list_tag_keys`; if `$1` is provided, filter the results to keys/values matching that pattern.

---

## Help (no args)

```
Prism — Colibri artifact hub

  /prism publish <file>         Upload an HTML, Markdown, or PDF file
  /prism link <url>             Register a SharePoint / Confluence / other link
  /prism search <query>         Search across all artifacts and collections
  /prism list [product|engineering|program|ux|users]
                                Browse collections, optionally by function
  /prism list users             Browse all personal user spaces
  /prism get <collection-path>  Show all items in a collection
  /prism restrict <path>        Restrict a collection to named emails
  /prism tag <path> team=martech        Add or update tags on a collection
  /prism untag <path> team             Remove a tag key from a collection
  /prism tags                          List all known tag keys and values
```

---

## publish

Guide the author through publishing a native artifact (HTML, Markdown, or PDF).

**Step 1 — Read the file**

If `$1` is a file path, infer mime type from extension (`.html` → `text/html`, `.md` → `text/markdown`, `.pdf` → `application/pdf`). If no path was given, ask:

```
AskUserQuestion: "Paste the file path to publish."
```

Check whether `curl` is available: run `command -v curl >/dev/null 2>&1 && echo "ok" || echo "missing"` via Bash. Then choose the upload tier:

| Condition | Tool to use in Step 7 |
|-----------|-----------------------|
| curl available (default) | `create_upload` + curl PUT + `finalize_upload` — presigned URL flow |
| curl missing (fallback only) | `publish_artifact` — read content with Read tool |

Always prefer the presigned URL flow. `publish_artifact` is a last resort when curl is not on the system.

**Step 2 — Function**

```
AskUserQuestion: "Which team owns this?"
  [product] [engineering] [program] [ux] [users — personal workspace]
```

If the user selects `users`, pre-fill the scope with their email prefix (first part before `@`). Skip the scope question and instead show: "Scope will be set to your username — `<email-prefix>`. Continue?"

**Step 3 — Archetype**

```
AskUserQuestion: "What type of content is this?"
  [reporting — recurring dashboards or outputs]
  [strategy — product, tech, or program strategy]
  [research — user research, discovery, competitive analysis]
  [analysis — data analysis, deep dives, investigations]
  [roadmap — planning artifacts by period or quarter]
```

If `function = users`, offer these archetypes instead:
```
AskUserQuestion: "What type of content is this?"
  [notes — personal notes, meeting notes, scratch pad]
  [experiments — prototypes, explorations, POCs]
  [portfolio — showcases, case studies, personal work]
  [drafts — works in progress, ideas in progress]
  [other — anything else]
```

If `reporting`, follow up:

```
AskUserQuestion: "How often is this published?"
  [daily] [weekly] [monthly] [quarterly]
```

**Step 4 — Scope**

Call `list_scopes`. Show the known scope slugs grouped for reference. Ask:

```
AskUserQuestion: "Which scope does this belong to?"
  [show up to 5 known scope slugs as options] [New scope…]
```

Call `upsert_scope` with `name` only (no `type` argument).

**Step 5 — Tags (optional)**

Call `list_tag_keys`. Using signals from the file already read in Step 1 (title, h1, team names, dates in headings or filename), suggest likely tags. Show the suggestion and ask:

```
AskUserQuestion: "Apply tags to this item?"
  [Use suggested: team=<inferred>, quarter=<inferred>]
  [Enter custom key=value pairs]
  [Skip — add tags later]
```

If the user picks suggested or custom, parse the input as comma-separated `key=value` pairs and pass as `tags` param to `publish_artifact`.

**Step 6 — Filename**

Derive based on archetype and frequency:
- `reporting/daily` → `YYYY-MM-DD.{ext}`
- `reporting/weekly` → `YYYY-WNN.{ext}`
- `reporting/monthly` → `YYYY-MM.{ext}`
- `reporting/quarterly` → `YYYY-QN.{ext}`
- `strategy/roadmap/research/analysis` → ask for a short slug label → `{label}.{ext}`

Show the proposed path and confirm:

```
AskUserQuestion: "Proposed path: product/checkout/reporting/2026-W22.html — correct?"
  [Yes, publish] [No, let me adjust]
```

**Step 7 — Publish**

Choose the path based on the size tier determined in Step 1:

**Default — curl available (presigned URL flow):**

1. Call `create_upload(path, mime_type)` → returns `{ upload_id, upload_url, content_type, expires_at }`.

2. Run the curl PUT — bytes go directly to S3, never through the model:
   ```bash
   curl -X PUT --upload-file "<local_path>" \
     -H "Content-Type: <content_type>" \
     "<upload_url>"
   ```
   Check the HTTP status. If not 200, stop and report the error.

3. Call `finalize_upload(upload_id, label, date_context, collection_function, scope_id, archetype, frequency, collection_title, tags)` → registers the artifact in the collection.

**Fallback — curl missing:**

Fall back to `publish_artifact` only if the preflight check in Step 1 returned `"missing"`. Read the file content with the Read tool and call `publish_artifact` with `content`, path, mime_type, label, date_context, collection_function, scope_id, archetype, frequency, collection_title, tags. Warn the user that this path is inefficient and suggest installing curl for future uploads.

Report back:
```
✓ Published to Prism
  Collection: product/checkout/reporting
  Item: 2026-W22.pdf
  View: https://prism.colibrigroup.tech/view/product/checkout/reporting/2026-W22.pdf
```

---

## link

Register an external document without uploading content.

Ask for the URL if not in `$1`. Then run the same function → archetype → scope flow as `publish` (using the updated Step 4: call `list_scopes`, show known scope slugs, ask for name, call `upsert_scope` with `name` only — no `type`). (If `users` is selected, apply the same scope auto-fill and archetype substitution described above.) Also ask:

```
AskUserQuestion: "What is the source?"
  [sharepoint] [confluence] [office365] [other]

AskUserQuestion: "Label for this link?" (suggest the URL's page title or last path segment)
```

After the path is confirmed, add the same optional tags step:

Call `list_tag_keys`. Infer likely tags from the URL, label, and any content signals available. Show the suggestion and ask:

```
AskUserQuestion: "Apply tags to this link?"
  [Use suggested: team=<inferred>, quarter=<inferred>]
  [Enter custom key=value pairs]
  [Skip — add tags later]
```

If the user picks suggested or custom, parse the input as comma-separated `key=value` pairs and pass as `tags` param to `add_link`.

Call `add_link` with collection_path, url, label, source, date_context, collection_function, scope_id, archetype, frequency, collection_title, and `tags` (if provided).

---

## search

Call `search_artifacts` with the remainder of `$@` as the query. Display results as a table:

```
Title                          Collection
─────────────────────────────  ──────────────────────────────
Week of May 19                 product/checkout/reporting
Q2 Strategy                    product/martech/strategy
```

If no results, say so and suggest broadening the query.

---

## list

Call `list_collections` with optional `function` filter from `$1` (if provided and valid). Display grouped by function, showing title, scope, archetype, and item count:

```
product
  Checkout Reporting        checkout    reporting    4 items
  Checkout Strategy         checkout    strategy     3 items
  Martech Weekly            martech     reporting    8 items

users
  Soley's Notes             soley       notes        3 items
  Aliza's Experiments       aliza       experiments  1 item
```

---

## get

Call `get_collection` with path = `$1`. Display the collection header (title, scope, archetype, owner, created), then list all items grouped by date, showing label, type (artifact/link), and added_by.

---

## restrict

Call `set_collection_acl` on `$1`. Ask:

```
AskUserQuestion: "Restrict to specific people, or open to all?"
  [Restrict to named emails]
  [Remove restriction — open to all]
```

If restricting, ask for a comma-separated list of email addresses. Call `set_collection_acl` with `is_restricted: true` and the parsed email list.

Confirm: `✓ Access updated — collection/path restricted to N addresses.`

---

## Tag inference

When reading a file before publish, extract these signals to suggest tags:

| Signal | Tag key | Example |
|---|---|---|
| Team/system name in title or h1 | `team` | "MarTech Observability" → `team=martech` |
| Date in filename or title | `quarter` | "2026-05-11" in W20 → `quarter=q2-2026` |
| Product name in headings | `product` | "Salesforce Marketing Cloud" → `product=sfmc` |
| Initiative name in content | `initiative` | "CDP Migration" → `initiative=cdp-migration` |

Always normalise values to lowercase-with-hyphens. Only suggest tags with high confidence — omit uncertain ones rather than guessing.

---

## Guidelines

- **Never invent a scope.** Always call `list_scopes` and show known options before prompting.
- **Always confirm the proposed path** before publishing or linking.
- **Reuse existing collections.** If `list_collections` shows a matching path, note it.
- **Labels must be slug-safe:** lowercase, hyphens only. Convert automatically.
- **Date context:** always populate for reporting artifacts — use today's date for daily, current ISO week for weekly, current month/quarter otherwise.
