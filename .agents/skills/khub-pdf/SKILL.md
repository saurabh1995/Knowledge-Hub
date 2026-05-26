---
name: khub-pdf
description: Read a PDF from the KnowledgeHub raw/ folder using a staged strategy — orientation pass first, then methods/results on request. Use when the user asks to read or preview a PDF before ingesting it, or when a PDF has more than 10 pages and needs staged reading before ingest.
compatibility: Read, Bash
---

You are operating inside Saurabh's KnowledgeHub. Read the PDF at `raw/$ARGUMENTS` using the staged strategy below. Do NOT read the full PDF in one pass.

---

## Staged PDF reading strategy

### Pass 1 — Orientation (always run first)

Read these sections in a single parallel batch:
- Pages 1–2 (title, authors, abstract, keywords)
- Last 2 pages (conclusions, future work)
- Any page containing "Table of Contents" or "Contents" if present (check p.3–4)

After Pass 1, report:
- Paper title, authors, year, venue
- Core claim in one sentence
- Method name / architecture
- Key metric (speedup, accuracy, energy)
- Hardware used
- Estimated total page count

**Pause here.** Ask: "Should I go deeper into methods, results, or a specific section?"

---

### Pass 2 — Methods & Results (on request)

Read these in parallel:
- Introduction (usually pp.3–5)
- Methods / Model Architecture section (use the TOC page numbers identified in Pass 1)
- Results / Experiments section
- Any page with a key results table or figure (use page numbers from Pass 1 TOC)

Summarise: approach, datasets, baselines compared, headline numbers, key table values. **Record the table/figure numbers and page numbers for all quantitative claims** — these will be needed as citations in the source page's `## Key claims & evidence` section.

**Pause here.** Ask: "Anything specific to drill into, or ready to ingest?"

---

### Pass 3 — Targeted drill-down (on request)

Read only the specific pages Saurabh names. Use the `pages` parameter precisely.

---

## After reading is complete

If Saurabh says to ingest, run `/khub-ingest $ARGUMENTS` — do not re-read the file; use the notes already extracted from the passes above.

---

**Note:** For PDFs under 10 pages, you may read all pages in Pass 1 and skip Pass 2/3.
