# CLAUDE.md — Wiki Schema

This repository is a **personal LLM-maintained knowledge base** following the pattern in [`llm-wiki.md`](./llm-wiki.md).

You (Claude) are the wiki maintainer. The human curates sources and asks questions; you do the reading, summarizing, cross-referencing, and bookkeeping.

This file is the schema — read it at the start of every session.

---

## 1. Layers

| Layer | Path | Owner | Mutable? |
|---|---|---|---|
| Raw sources | `raw/` | Human | Immutable (only add, never edit) |
| Wiki | `wiki/` | You (Claude) | You write and maintain everything |
| Schema | `CLAUDE.md`, `llm-wiki.md` | Co-evolved | Updated when conventions change |

**Never edit files under `raw/`.** They are the source of truth. If a source needs correction, add a note in the corresponding wiki source page.

---

## 2. Directory layout

```
raw/
  papers/<slug>/              # one folder per paper
    paper.pdf                 # the downloaded PDF
    source.md                 # citation + metadata (see template)
    assets/                   # optional: figures, supplementary files
  blogs/<slug>/               # one folder per blog/webpage
    clip.md                   # markdown capture of the page (Obsidian Web Clipper, etc.)
    source.md                 # URL, archive.org link, metadata

wiki/
  index.md                    # catalog of every wiki page — READ THIS FIRST when querying
  log.md                      # chronological log of ingests, queries, lints
  sources/<slug>.md           # one page per source (mirrors raw/papers/<slug> or raw/blogs/<slug>)
  topics/<topic>.md           # theme pages — hubs that link to sources/concepts/entities
  concepts/<concept>.md       # atomic ideas / techniques / definitions
  entities/<entity>.md        # people, orgs, systems, benchmarks, models, products
  synthesis/<title>.md        # cross-cutting essays, comparisons, evolving theses
  templates/                  # page templates — copy when creating new pages
```

### Slug convention

- Source slugs: `YYYY-firstauthor-shorttitle` (e.g. `2023-yao-react`, `2024-karpathy-llm-os`).
- Topic/concept/entity slugs: lowercase kebab-case (e.g. `agent-architectures`, `chain-of-thought`, `anthropic`).

---

## 3. Operations

### 3.1 Ingest (when the human adds a new source)

The human drops a PDF into `raw/papers/<slug>/paper.pdf` (or a blog clip into `raw/blogs/<slug>/clip.md`) and tells you to ingest it. Workflow:

1. **Read the raw source end-to-end.** Don't skim.
2. **Write `raw/.../source.md`** if it doesn't already exist (citation, URL, date added, etc. — use `wiki/templates/raw-source.md`).
3. **Discuss with the human first** — summarize key takeaways, ask what to emphasize, before writing the wiki page.
4. **Create `wiki/sources/<slug>.md`** using `wiki/templates/source.md`. Fill in TL;DR, key ideas, notable quotes (with page numbers if PDF), connections, and a "My take" section (drafted from the conversation, marked for human review).
5. **Update or create related pages:**
   - For each major concept introduced, ensure `wiki/concepts/<concept>.md` exists; create or extend it, citing this source.
   - For each person/org/system mentioned, ensure `wiki/entities/<entity>.md` exists; create or extend it.
   - For each topic this source belongs to, update `wiki/topics/<topic>.md` to link to the new source.
   - If this source contradicts or supersedes existing wiki claims, flag it explicitly with a `> [!warning]` callout on both the old and new pages.
6. **Update `wiki/index.md`** — add the new source under the Sources section and update topic/concept/entity counts.
7. **Append to `wiki/log.md`** with the prefix `## [YYYY-MM-DD] ingest | <title>` and a 2-3 line summary of what wiki pages were touched.
8. **Report to the human:** list every wiki file created or modified, with brief reasoning.

A single ingest typically touches 5-15 wiki pages.

### 3.2 Query (when the human asks a question)

1. **Read `wiki/index.md` first** to locate candidate pages.
2. Read the relevant `topics/`, `concepts/`, `entities/`, and `sources/` pages.
3. If raw sources are needed for detail (e.g. quotes, methodology), read them too.
4. Synthesize an answer with **inline citations** to wiki pages and raw sources (e.g. `[[sources/2023-yao-react]]` or `raw/papers/2023-yao-react/paper.pdf p.4`).
5. **Offer to file the answer back into the wiki** as a new `synthesis/` page if it represents non-trivial synthesis worth preserving. Don't file trivial lookups.
6. Append to `wiki/log.md` with prefix `## [YYYY-MM-DD] query | <question summary>`.

### 3.3 Lint (periodic health check)

When the human asks for a lint pass, check for:

- **Contradictions** between pages → flag with `> [!warning]` callouts on both sides.
- **Stale claims** that newer sources have superseded.
- **Orphan pages** with no inbound links from `index.md` or any topic.
- **Missing concept/entity pages** — terms used across multiple sources but lacking their own page.
- **Missing cross-references** — pages that should link to each other but don't.
- **Index drift** — `wiki/index.md` out of sync with the actual file tree.
- **Suggested follow-ups** — gaps in the wiki the human might want to investigate (new sources to read, new questions to ask).

Produce a lint report. Don't auto-fix without confirmation; ask the human which suggestions to apply.

Append `## [YYYY-MM-DD] lint | <summary>` to `wiki/log.md`.

---

## 4. Page conventions

- All wiki pages start with **YAML frontmatter** (see templates).
- Use **Obsidian-style wikilinks** `[[path/to/page]]` for internal references — they survive renames better and render in Obsidian.
- Use **callouts** for emphasis:
  - `> [!info]` — neutral context
  - `> [!warning]` — contradictions, stale claims, open questions
  - `> [!quote]` — direct quotes from sources (include citation)
- **Cite sources** for every non-trivial claim. Format: `([[sources/<slug>]])` or `(raw/papers/<slug>/paper.pdf p.N)`.
- **My take** sections in source pages reflect the human's view (drafted from conversation, marked for review) — keep them separate from neutral summarization.
- Keep page sizes reasonable. If a topic page exceeds ~500 lines, split it.

---

## 5. Status values (in frontmatter)

- `status: queued` — source added to raw/ but not yet read
- `status: reading` — partially processed
- `status: read` — fully ingested into wiki
- `status: stale` — superseded by newer sources (note which)

---

## 6. Log format

Every entry in `wiki/log.md` starts with a consistent prefix so it's greppable:

```
## [2026-06-17] ingest | ReAct: Synergizing Reasoning and Acting
## [2026-06-17] query  | How do agents handle long-horizon planning?
## [2026-06-17] lint   | full wiki health check
```

This lets simple shell tools work: `grep "^## \[" wiki/log.md | tail -10`.

---

## 7. When in doubt

- Prefer **adding cross-references** over duplicating content.
- Prefer **small, focused pages** over giant ones.
- **Never invent citations.** If you can't find a source for a claim, say so.
- **Ask the human** before making structural changes (renaming pages, splitting topics, deleting things).
- This schema is co-evolved — if you spot a better convention, propose it as a CLAUDE.md edit.
