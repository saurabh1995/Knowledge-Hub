---
name: khub-ingest
description: Ingest a new source document into the KnowledgeHub wiki. Use when the user says "ingest [filename]" or asks to process/add a new paper or document. Do NOT use for queries, lint, or health-check operations.
compatibility: Read, Grep, Write, Edit, Bash
---

You are operating inside Saurabh's KnowledgeHub. Follow CLAUDE.md exactly. Execute the INGEST workflow for the file: $ARGUMENTS

**Usage:** pass just the filename, e.g. `/khub-ingest my-paper.pdf`. Do not include the `raw/` prefix.

---

## Step 1 — Read the source

Read the file from `raw/$ARGUMENTS`. If it is a PDF larger than 10 pages, use `/khub-pdf $ARGUMENTS` staged strategy instead of reading all at once.

## Step 2 — Discuss (GATE — do not proceed past here without user confirmation)

Present to Saurabh:
- 3–5 key takeaways from the source
- Which existing wiki pages this source touches (grep wiki/ for relevant terms before answering)
- What is genuinely new vs. already covered
- Proposed plan: source page slug, entity pages to update, concept pages to update/create

**Wait for Saurabh's go-ahead before writing anything.**

## Step 3 — Execute (only after confirmation)

Work through each step in order. Mark each as done before moving to the next.

- [ ] **3a. Create** `wiki/sources/<slug>.md` using the template below
- [ ] **3b. Update** entity pages in `wiki/entities/` that are mentioned
- [ ] **3c. Update** concept pages in `wiki/concepts/` that are relevant
- [ ] **3d. Create** new entity/concept pages for anything important without a page
- [ ] **3e. Update** `wiki/overview.md` if the source shifts the big picture
- [ ] **3f. Update** `wiki/index.md` — add source row and any new pages
- [ ] **3g. Update** `wiki/quick-ref.md` — add source row to papers table, any new numeric claims, update concept→source mapping, and add any contradicting values to the `## Known contradictions / parameter variants` section
- [ ] **3h. Validate wikilinks** — grep all pages written/updated in this ingest for `\[\[` patterns; confirm every linked title exists in `wiki/index.md`; create missing stubs or remove the link before proceeding
- [ ] **3i. Append** entry to `wiki/log.md`

---

## Source page template

```markdown
---
title: "Source: <Full Title>"
type: source
tags: []
created: <today>
updated: <today>
sources: 1
---
**One-line summary.**

## Key points
- ...

## Methodology / approach
...

## Key claims & evidence
_Each quantitative claim must cite the PDF location — section, table, or page number. E.g. "60% faster than CPU (Table 4, p. 8)". This is required for verifiability._
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

**Reminder:** Never modify files in `raw/`. Never create files outside `wiki/`. Flag contradictions explicitly — do not silently overwrite old claims.
