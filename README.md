# KnowledgeHub — LLM-Maintained Personal Wiki

A persistent, compounding personal knowledge base where an AI CLI agent (Claude Code, Codex, or Gemini CLI) acts as a **wiki editor**: ingesting papers and articles, maintaining structured pages, cross-referencing concepts, and answering questions with full inline citations — all from a local folder of markdown files.

The human curates sources and asks questions. The AI does all the writing, cross-referencing, and bookkeeping.

---

## What this is

KnowledgeHub is a **two-layer system**:

| Layer | Location | Who owns it |
|-------|----------|-------------|
| `raw/` | Source documents: PDFs, clipped articles, notes | You (read-only for the AI) |
| `wiki/` | Structured summary pages, concept pages, entity pages, index, log | The AI agent |

The wiki is a **compiled artifact**, not a retrieval index. Knowledge is integrated once and kept current. When you ask a question, the AI greps the wiki — it does not re-read the raw PDFs every time.

---

## Directory layout

```
K-hub (YourName)/
├── CLAUDE.md               ← Schema + rules (the AI's constitution)
├── README.md               ← This file
├── raw/                    ← Drop your papers and articles here
│   ├── assets/             ← Images referenced by sources
│   └── *.pdf  *.md  *.txt  ← Source documents (never modified by AI)
└── wiki/                   ← Entirely maintained by the AI
    ├── index.md            ← Master catalog of all pages
    ├── log.md              ← Append-only operation log
    ├── overview.md         ← High-level synthesis across all sources
    ├── quick-ref.md        ← Flat facts table (primary grep target for queries)
    ├── sources/            ← One summary page per ingested source
    ├── entities/           ← People, organisations, tools, datasets
    └── concepts/           ← Ideas, frameworks, theories, methods
```

---

## Current wiki contents (as of April 2026)

- **17 sources** — papers on NN-enhanced FEM, spiking neural networks, neuromorphic computing, binary NNs, FPGA acceleration, and cell imaging (2020–2026)
- **7 entities** — researchers including Saurabh Balkrishna Tandale, Marcus Stoffel, and collaborators
- **17 concepts** — neural-network-enhanced FEM, self-learning NNs, spiking neural networks, viscoplasticity modelling, Sobolev training, MAML meta-learning, and more
- **46 pages total**

Domain: computational mechanics, ML-accelerated finite element methods, neuromorphic computing, mechanobiology.

---

## Setup — adapt for your own vault

1. Copy this folder structure (or clone the repo).
2. Replace `raw/` with your own source documents.
3. Clear `wiki/` (or keep it as a reference). Update `wiki/index.md` and `wiki/log.md` accordingly.
4. Edit `CLAUDE.md` — change the owner name and adjust the domain description if needed.
5. Follow the AI CLI instructions below to point your tool at the vault.

---

## Using with Claude Code

Claude Code reads `CLAUDE.md` automatically when you open a session in the vault directory. No extra flags needed.

### Setup

```bash
# Install Claude Code
npm install -g @anthropic-ai/claude-code

# Open a session in the vault directory
cd "path/to/K-hub (YourName)"
claude
```

Claude Code picks up `CLAUDE.md` and runs the session-start checklist automatically. It will greet you with the wiki status.

### Custom skills (optional)

This vault uses Claude Code skills defined in `.claude/commands/`. If you have custom slash commands (e.g. `/khub-ingest`, `/khub-query`), they will be available automatically. See [Claude Code docs](https://docs.anthropic.com/claude-code) for how to create skills.

### Session flow

```
You:    ingest Tandale_2024_CMAME.pdf
Claude: [reads PDF, discusses key takeaways, proposes wiki plan]
You:    looks good, go ahead
Claude: [creates/updates 8–12 wiki pages, validates all wikilinks, logs the operation]

You:    what speedup does the BNN achieve on FPGA vs CPU?
Claude: 60% faster than CPU (Table 3, §4.2) ([[sources/fpga-bnn-viscoplastic-mrc-2025]])

You:    lint
Claude: [scans for broken wikilinks, orphan pages, contradictions, stale claims]
```

---

## Using with OpenAI Codex CLI

The Codex CLI (`codex`) uses a system prompt file. Adapt `CLAUDE.md` as a system prompt.

### Setup

```bash
# Install Codex CLI
npm install -g @openai/codex

# Run with the CLAUDE.md as system instructions
cd "path/to/K-hub (YourName)"
codex --system-prompt "$(cat CLAUDE.md)"
```

Or create a `codex.config.json` in the vault root:

```json
{
  "systemPrompt": "path/to/CLAUDE.md",
  "model": "o4-mini"
}
```

### Adapting CLAUDE.md for Codex

`CLAUDE.md` is written for Claude Code's tool set (Grep, Read, Write, Edit). Codex uses shell commands instead. Add a note at the top of your Codex system prompt:

```
TOOL MAPPING (Codex shell mode):
- Grep → rg or grep -r
- Read → cat
- Write → write file contents directly
- Glob → find . -name "pattern"
When searching multiple directories, run grep commands in parallel using & and wait.
```

### Session flow

```bash
codex "ingest Papers/my-paper-2025.pdf"
codex "what is the energy efficiency of the Xylo-Av2 chip?"
codex "lint"
```

---

## Using with Gemini CLI

Gemini CLI supports a `GEMINI.md` instructions file (analogous to `CLAUDE.md`).

### Setup

```bash
# Install Gemini CLI
npm install -g @google/gemini-cli

# Gemini CLI auto-reads GEMINI.md if present
# Option 1: symlink or copy CLAUDE.md → GEMINI.md
cp CLAUDE.md GEMINI.md

# Option 2: pass it as a system prompt
gemini --system "$(cat CLAUDE.md)"

# Start a session in the vault directory
cd "path/to/K-hub (YourName)"
gemini
```

### Adapting CLAUDE.md for Gemini

Gemini CLI's tool set differs from Claude Code's. Add a tool mapping note at the top of `GEMINI.md`:

```
TOOL MAPPING (Gemini CLI):
- Use built-in file read/write tools for Read/Write/Edit operations.
- Use built-in grep/search for Grep operations.
- When instructed to "grep in parallel", run multiple searches before synthesising.
- All other schema rules (page format, workflows, naming) apply unchanged.
```

### Session flow

```
You:    ingest raw/Papers/my-paper-2025.pdf
Gemini: [reads PDF, summarises, creates wiki pages]

You:    explain the Lemaitre-Chaboche model
Gemini: [greps quick-ref.md, then concepts/viscoplasticity-modelling.md, answers with inline citations]
```

---

## The three core operations

### INGEST — add a new source

Trigger: `ingest <filename>`

The AI will:
1. Read the raw source file
2. Discuss key takeaways with you and propose a plan
3. *(After your approval)* Create `wiki/sources/<slug>.md`
4. Update relevant `wiki/entities/` and `wiki/concepts/` pages
5. Create new entity/concept pages for anything important that lacks one
6. Update `wiki/overview.md`, `wiki/index.md`, `wiki/quick-ref.md`
7. Validate every `[[wikilink]]` — no dangling references allowed
8. Append to `wiki/log.md`

Each ingest typically touches 5–15 wiki pages. Done in one session.

### QUERY — ask a question

Trigger: any question about the domain

The AI will:
1. Grep `wiki/quick-ref.md` first (fastest path)
2. If needed, grep `wiki/sources/`, `wiki/concepts/`, `wiki/entities/` in parallel
3. Verify every number, date, and model name against the wiki before stating it
4. Answer with **inline per-claim attribution**: `Claim X ([[Page]])`
5. Label any inference combining ≥2 pages as `[SYNTHESIS]`
6. Offer to file a good answer as a new concept page

If the wiki doesn't contain the answer, the AI checks `raw/`, attempts synthesis, or declares a gap — it never hallucinates citations.

### LINT — health check

Trigger: `lint` or `health-check`

The AI will scan for:
- Broken wikilinks (referenced titles that don't exist)
- Orphan pages (no inbound links)
- Contradictions between pages
- Stale claims superseded by newer sources
- Missing pages for mentioned concepts/entities

Fix issues you approve, then log the operation.

---

## Wiki page formats

### Source page (`wiki/sources/<slug>.md`)

```markdown
---
title: "Source: Article Title"
type: source
tags: [FEM, SNN, neuromorphic]
created: YYYY-MM-DD
updated: YYYY-MM-DD
sources: 1
---
**One-line summary.**

## Key points
## Methodology / approach
## Key claims & evidence
## Limitations / caveats
## Contradictions with existing wiki
## Quotes worth keeping
## See also
```

### Concept page (`wiki/concepts/<name>.md`)

```markdown
---
title: "Concept Name"
type: concept
tags: []
created: YYYY-MM-DD
updated: YYYY-MM-DD
---
**One-line summary.**

[Body content]

## Mathematical formulation        ← optional; LaTeX math fences ($$...$$)
## See also
```

### Entity page (`wiki/entities/<name>.md`)

```markdown
---
title: "Person / Org / Tool Name"
type: entity
tags: []
created: YYYY-MM-DD
updated: YYYY-MM-DD
---
**One-line summary.**

[Body content]

## See also
```

---

## Key rules the AI follows

| Rule | Detail |
|------|--------|
| Grep before Read | Extract lines via grep; open full pages only when needed |
| Grep in parallel | All directory searches run simultaneously |
| Inline attribution | Every claim: `Claim ([[Source]])` — no footer-only citations |
| Label synthesis | Cross-page inferences prefixed `[SYNTHESIS]` |
| Verify numbers | Every figure, date, model name grepped before stating |
| No hallucinated links | Only cite pages in `wiki/index.md` |
| Immutable raw/ | Never modify files in `raw/` |
| Append-only log | Never delete or edit past `log.md` entries |
| One ingest at a time | Unless batch mode explicitly requested |

---

## Adapting to your own domain

KnowledgeHub is domain-agnostic. The current instance focuses on computational mechanics and neuromorphic AI, but the schema works for any research area:

1. Drop your PDFs/articles into `raw/`
2. Start a fresh `wiki/index.md`, `wiki/log.md`, `wiki/quick-ref.md`, `wiki/overview.md`
3. Update the description in `CLAUDE.md` §1 to reflect your domain
4. Begin ingesting sources one by one

The AI builds the wiki incrementally. After 5–10 ingests, the quick-ref table and cross-linked concept pages make querying significantly faster than re-reading raw PDFs.

---

## File naming conventions

| Type | File path | `title` frontmatter |
|------|-----------|---------------------|
| Source summary | `wiki/sources/slug-of-title.md` | `"Source: Full Title"` |
| Entity | `wiki/entities/name.md` | `"Name"` |
| Concept | `wiki/concepts/name.md` | `"Name"` |
| Overview | `wiki/overview.md` | `"Overview"` |

Slugs: lowercase, hyphens only, no special characters, max 50 characters.

---

## Troubleshooting

**AI creates files outside `wiki/`**
The AI must never write to `raw/` or the vault root (except `CLAUDE.md` updates). If it does, undo and remind it of the rule in §2 of `CLAUDE.md`.

**Broken wikilinks after ingest**
Run `lint` — the AI will find and fix all dangling `[[links]]`.

**Codex/Gemini ignores the schema**
Paste the full contents of `CLAUDE.md` as the first user message, or into the system prompt config. The schema is designed to be self-contained and tool-agnostic.

**AI answers from memory instead of the wiki**
Remind it: "Grep quick-ref.md first. Do not answer from training data." Inline citations should appear in every factual answer.

---

## License

This vault schema (`CLAUDE.md`, `README.md`) is available for reuse and adaptation. The `wiki/` and `raw/` contents belong to Saurabh Balkrishna Tandale and are not redistributed.
