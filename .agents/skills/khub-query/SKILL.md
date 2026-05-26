---
name: khub-query
description: Answer any factual question from the KnowledgeHub wiki. Use this skill for ANY question, lookup, comparison, or synthesis request — i.e. whenever the user asks "what", "how", "why", "who", "compare", "describe", "explain", "tell me about", "list", or any similar knowledge query directed at the wiki. Do NOT use for ingest, lint, or health-check operations.
compatibility: Grep, Read, Bash
---

# KnowledgeHub Query Workflow

This skill implements the "QUERY" workflow defined in `AGENTS.md` section 7.

## Workflow Steps

### Primary path

1. **Quick-ref check**: Grep `wiki/quick-ref.md` for the query terms. If a definitive
   answer is found, return it immediately with inline attribution and skip to step 6.

2. **Parallel deep search**: If quick-ref is insufficient, run all three greps
   **simultaneously** (not sequentially):
   - `wiki/sources/`
   - `wiki/concepts/`
   - `wiki/entities/`

3. **Detailed inspection**: If the grep excerpts are still insufficient, use `Read` on
   the most relevant pages identified in step 2.

4. **Verification pass**: For every specific number, model name, or date in the drafted
   answer, run a `grep` to confirm it exists in the wiki. If a grep returns no match,
   mark the claim `[UNCERTAIN — not found in wiki]` or drop it. Never state unverified
   figures as fact.

5. **Synthesis & attribution**:
   - **Inline attribution**: Every factual claim must be followed immediately by its
     source: `Claim text ([[Page Title]])`. Do not list citations at the footer only.
   - **Synthesis label**: Any claim derived by combining ≥2 pages must be prefixed
     `[SYNTHESIS]` so the user knows it is model inference, not a direct wiki fact.
   - **Index validation**: Only emit wikilinks for pages that appear in `wiki/index.md`.
     If you would cite a page not in the index, point the user to the raw file instead
     or declare a gap.

### Fallback path (when steps 1–3 return nothing or insufficient results)

Work through these in order — stop at the first that yields an answer:

- **3a. Check `raw/`** — grep `raw/` for the query terms. If a match is found, report
  which file contains it and offer to ingest it or do a targeted read. Do not silently
  skip unprocessed sources.

- **3b. Attempt synthesis** — can the answer be derived by combining ≥2 existing wiki
  pages? If yes, list each contributing fact and its source page *before* combining
  them, label the combined result `[SYNTHESIS]`, and offer to file it as a new concept
  page. Never blend facts silently.

- **3c. Declare the gap** — if `raw/` also misses and synthesis is not possible, say
  so explicitly: name the missing topic and suggest what type of source would fill it.
  Do not guess or hallucinate an answer.

- **3d. Log the miss** — append to `wiki/log.md` as a `query` entry noting the gap:
  `gap identified: [topic]`.

### Finalisation (always run)

6. **File or skip**: Ask the user: "Should I file this answer as a new wiki page?"
   Good analyses, comparisons, and discoveries belong in `wiki/concepts/`.

7. **Log**: Append a `query` entry to `wiki/log.md`.

## Output Format

```
[Answer with mandatory inline citations and [SYNTHESIS] prefixes where applicable]

Should I file this answer as a new wiki page?
```

## Example

**User**: "What is the performance of the new model?"

**Skill Execution**:
1. `grep "performance" wiki/quick-ref.md` → finds "Model X is 20% faster (Source: Article A)".
2. Return: "Model X provides a 20% speedup ([[Source: Article A]])."
3. Ask: "Should I file this answer as a wiki page?"
4. Log to `wiki/log.md`.
