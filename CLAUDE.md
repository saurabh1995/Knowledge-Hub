# KnowledgeHub Schema — CLAUDE.md

This file is the authoritative schema for Saurabh's KnowledgeHub. Every Claude Code
session operating in this vault MUST read this file first and follow all rules below.

---

## 1. What this is

A persistent, compounding personal knowledge base. The LLM (you) maintains a wiki of
structured markdown files. The human (Saurabh) curates sources, directs analysis, and
asks questions. You do all the writing, cross-referencing, and bookkeeping.

**Golden rule:** the wiki is a compiled artifact, not a retrieval index. Knowledge is
integrated once and kept current — not re-derived on every query.

---

## 2. Directory layout

```
K-hub (Saurabh)/
├── CLAUDE.md                  ← this file (schema + rules)
├── raw/                       ← immutable source documents (you READ, never modify)
│   ├── assets/                ← downloaded images referenced by sources
│   └── *.md  *.pdf  *.txt     ← clipped articles, papers, notes
└── wiki/                      ← you OWN this layer entirely
    ├── index.md               ← master catalog of all wiki pages
    ├── log.md                 ← append-only operation log
    ├── overview.md            ← evolving high-level synthesis
    ├── quick-ref.md           ← flat facts table: models, speedups, hardware (primary grep target)
    ├── sources/               ← one summary page per ingested source
    ├── entities/              ← people, organisations, tools, datasets
    └── concepts/              ← ideas, frameworks, theories, methods
```

**Never create files outside `wiki/`.** Never modify files in `raw/`.

---

## 3. Page format & frontmatter

Every wiki page (except `index.md` and `log.md`) MUST begin with YAML frontmatter:

```yaml
---
title: "Page Title"
type: source | entity | concept | overview
tags: [tag1, tag2]
created: YYYY-MM-DD
updated: YYYY-MM-DD
sources: 0          # number of raw sources this page draws from (omit for non-source types)
---
```

After frontmatter, pages follow this structure:
- **One-line summary** (bold, right after frontmatter)
- Body content (headings, prose, lists, tables as appropriate)
- **## See also** section at the bottom with `[[wikilinks]]` to related pages

**Wikilinks:** always use `[[Page Title]]` (matching the `title` frontmatter) for
cross-references. Never use raw file paths.

---

## 4. index.md format

`index.md` is the master catalog. Updated on every ingest and whenever new pages are
created. Structure:

```markdown
# KnowledgeHub Index
_Last updated: YYYY-MM-DD — N pages total_

## Overview
- [[Overview]] — evolving synthesis of all knowledge

## Sources (N)
| Page | Summary | Date | Tags |
|------|---------|------|------|
| [[sources/Title]] | one line | YYYY-MM-DD | tag, tag |

## Entities (N)
| Page | Type | Summary |
|------|------|---------|
| [[entities/Name]] | person/org/tool | one line |

## Concepts (N)
| Page | Summary |
|------|---------|
| [[concepts/Name]] | one line |
```

---

## 5. log.md format

`log.md` is append-only. NEVER delete or edit past entries. Each entry:

```markdown
## [YYYY-MM-DD] operation | Title or description

- Key action 1
- Key action 2
- Pages created: [[page1]], [[page2]]
- Pages updated: [[page3]]
```

Operation types: `ingest`, `query`, `lint`, `manual`.

Grep tip: `grep "^## \[" wiki/log.md | tail -10` shows the 10 most recent entries.

---

## 6. Workflow: INGEST

When Saurabh says "ingest [filename]" or drops a new source:

1. **Read** the source file from `raw/`.
2. **Discuss** with Saurabh: key takeaways, what to emphasise, how it relates to existing wiki.
3. **Create** `wiki/sources/<slug>.md` — a structured summary page.
4. **Update** existing entity pages in `wiki/entities/` that are mentioned.
5. **Update** existing concept pages in `wiki/concepts/` that are relevant.
6. **Create** new entity or concept pages for anything important that lacks a page.
7. **Update** `wiki/overview.md` if the source shifts the big picture.
8. **Update** `wiki/index.md` — add the new source and any new pages.
9. **Update** `wiki/quick-ref.md` — add the new source row to the papers table and any new numeric claims.
10. **Validate wikilinks** — grep all newly created/updated pages for `\[\[` patterns. For every wikilink, confirm the target title appears in `wiki/index.md`. If any link is unresolved, either create the missing stub page immediately or remove the link. Do not close the ingest with dangling wikilinks.
11. **Append** an entry to `wiki/log.md`.

Each ingest typically touches 5–15 wiki pages. Do all of this in one session.
After step 2, confirm the plan with Saurabh before writing.

**Source page structure:**
```markdown
---
title: "Source: Article Title"
type: source
tags: []
created: YYYY-MM-DD
updated: YYYY-MM-DD
sources: 1
---
**One-line summary.**

## Key points
- ...

## Methodology / approach
...

## Key claims & evidence
_Each quantitative claim must include the PDF location (paper section, table, or page number) so it can be verified against the raw file. Example: "60% faster than CPU (Table 4, p. 8)"._
...

## Limitations / caveats
...

## Contradictions with existing wiki
...

## Quotes worth keeping
> "..."

## See also
[[concept]], [[entity]]
```

---

## 7. Workflow: QUERY

When Saurabh asks a question:

1. **Grep `wiki/quick-ref.md` first** — it contains all key claims, models, hardware, and concept→source mappings. If it answers the question, stop here.
2. If more detail is needed, **grep in parallel**: run Grep simultaneously across `wiki/sources/`, `wiki/concepts/`, and `wiki/entities/` — do not run these sequentially.
3. Only read a full page if parallel grep excerpts are still insufficient.
4. **Verification pass before answering** — after drafting the answer, grep for every specific number, model name, and date you intend to state. If a grep returns no match in the wiki, drop the claim or mark it `[UNCERTAIN — not found in wiki]`. Do not state it as fact.
5. **Synthesise with inline per-claim attribution** — every factual claim must carry its source immediately: `Claim X ([[Page]])`. Do not list citations only at the footer. Any claim derived by combining ≥2 pages (rather than directly stated in one) must be prefixed `[SYNTHESIS]` so the user knows it is model inference, not a wiki fact. **Only cite pages whose titles appear in `wiki/index.md`.** If you would cite a page that is not indexed, direct the user to the raw source file instead or declare a gap — never emit a wikilink that does not exist.
6. Ask: "Should I file this answer as a wiki page?" — good analyses, comparisons, and
   discoveries should be saved back into the wiki (in `wiki/concepts/` or a new
   subfolder if needed).
7. Append to `wiki/log.md` with operation type `query`.

### Fallback: when the wiki does not answer

If steps 1–3 return nothing or insufficient results, work through these in order — stop at the first that yields an answer:

- **3a. Check `raw/`** — grep `raw/` for the query terms. If a match is found, report which file contains it and offer to ingest it or do a targeted read. Do not silently skip unprocessed sources.
- **3b. Attempt synthesis** — can the answer be derived by combining ≥2 existing wiki pages? If yes, list each contributing fact and its source page *before* combining them, label the combined result `[SYNTHESIS]`, and offer to file it as a new concept page. Never blend facts silently.
- **3c. Declare the gap** — if `raw/` also misses and synthesis is not possible, say so explicitly: name the missing topic and suggest what type of source would fill it. Do not guess or hallucinate an answer.
- **3d. Log the miss** — append to `wiki/log.md` as a `query` entry noting the gap: `gap identified: [topic]`.

---

## 8. Workflow: LINT

When Saurabh says "lint" or "health-check":

1. **Grep first** — scan `wiki/` for issues without reading full pages:
   - Broken wikilinks: grep for `\[\[` patterns and verify targets exist in index
   - Orphans: grep index for pages not referenced elsewhere
   - Contradictions/stale claims: grep for key numeric claims and dates across sources
   - Only read full pages for items flagged as needing deeper inspection
2. Report:
   - **Contradictions** between pages
   - **Stale claims** superseded by newer sources
   - **Orphan pages** with no inbound `[[wikilinks]]`
   - **Missing pages** for concepts/entities mentioned but without their own page
   - **Broken wikilinks** (referenced titles that don't exist)
   - **Data gaps** that a web search could fill
3. Suggest new sources to look for.
4. Fix issues Saurabh approves; append to `wiki/log.md`.

---

## 9. Naming conventions

| Type | File path | Title frontmatter |
|------|-----------|-------------------|
| Source summary | `wiki/sources/slug-of-title.md` | `"Source: Full Title"` |
| Entity | `wiki/entities/name.md` | `"Name"` |
| Concept | `wiki/concepts/name.md` | `"Name"` |
| Overview | `wiki/overview.md` | `"Overview"` |

Slugs: lowercase, hyphens, no special characters. Max 50 chars.

---

## 10. Behavioural rules

- **For queries: grep `wiki/quick-ref.md` first** — do not read `wiki/index.md` for queries; it is only needed for ingest and lint.
- **Grep before Read** — for queries and lint, extract relevant lines via Grep first; open full pages only when excerpts are insufficient to answer.
- **Grep in parallel** — when searching multiple directories, issue all Grep calls simultaneously, not sequentially.
- **Never hallucinate citations.** Only reference pages that exist in the wiki.
- **Inline attribution, not footer citations** — in query answers, every factual claim must be followed immediately by its source `([[Page]])`. A list of sources at the end is not sufficient.
- **Label synthesis explicitly** — any claim inferred by combining ≥2 pages must be prefixed `[SYNTHESIS]`. A user must always be able to tell whether a statement is a wiki fact or model inference.
- **Verify numbers before stating them** — grep for every specific number, date, or model name before including it in a query answer. If the grep returns no match, drop the claim or mark it `[UNCERTAIN]`.
- **Prefer updating existing pages** over creating redundant new ones.
- **Keep source pages factual.** Save your own synthesis for concept/overview pages.
- **Flag contradictions explicitly** — don't silently overwrite old claims.
- **Ask before bulk-deleting** anything from the wiki.
- **One ingest at a time** unless Saurabh explicitly requests batch mode.
- At session start, read this file and `wiki/log.md` (last 20 entries) to restore context.

---

## 11. Session start checklist

Every new Claude Code session:
1. Read `CLAUDE.md` (this file). ✓
2. Grep `wiki/log.md` for `^## \[` to see recent operation headers; read last ~50 lines only if detail is needed.
3. Read `wiki/quick-ref.md` — sufficient for query sessions; read `wiki/index.md` only if the session involves ingest or lint.
4. Greet User with a one-line status: "Wiki has N pages. Last operation: [type] on [date]."
5. Await instructions.
