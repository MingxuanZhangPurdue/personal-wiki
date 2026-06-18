# Personal Reading Knowledge Base

A personal LLM-maintained knowledge base of papers, blogs, and other reading. Built on the pattern in [`llm-wiki.md`](./llm-wiki.md).

I curate sources. An LLM agent (Claude Code) maintains the wiki.

## How it works

Three layers:

- **`raw/`** — immutable source documents (PDFs of papers, markdown clips of blogs). Never edited after adding.
- **`wiki/`** — LLM-generated, LLM-maintained markdown: per-source summaries, topic hubs, concept pages, entity pages, cross-cutting syntheses, an index, and a log.
- **`CLAUDE.md`** — the schema. Tells the LLM how to ingest sources, answer queries, and lint the wiki.

## Workflow

### Add a paper

```
raw/papers/2023-yao-react/
  paper.pdf
  source.md          # citation + metadata
```

Then in Claude Code: _"Ingest `raw/papers/2023-yao-react/`."_

### Add a blog / webpage

```
raw/blogs/2024-karpathy-llm-os/
  clip.md            # markdown copy of the page (Obsidian Web Clipper, or paste)
  source.md          # URL + archive URL + metadata
```

Then: _"Ingest `raw/blogs/2024-karpathy-llm-os/`."_

Saving the page locally as `clip.md` protects against link rot. Record the original URL and a [Wayback Machine](https://web.archive.org/save) snapshot in `source.md`.

### Ask a question

In Claude Code: _"What does the wiki say about <topic>?"_

It reads [`wiki/index.md`](./wiki/index.md), drills into relevant pages, and answers with citations. Good answers get filed back into `wiki/synthesis/` so the analysis compounds.

### Periodic lint

_"Run a lint pass on the wiki."_ — surfaces contradictions, stale claims, orphan pages, missing cross-references, and follow-up suggestions.

## Layout

```
.
├── CLAUDE.md              # schema — read first every session
├── llm-wiki.md            # the pattern this repo instantiates
├── README.md              # this file
├── raw/                   # immutable sources
│   ├── papers/<slug>/     # paper.pdf + source.md
│   └── blogs/<slug>/      # clip.md + source.md
└── wiki/                  # LLM-owned
    ├── index.md           # catalog — query entry point
    ├── log.md             # chronological activity log
    ├── sources/           # one page per source
    ├── topics/            # theme hubs
    ├── concepts/          # atomic ideas
    ├── entities/          # people, orgs, systems, models, benchmarks
    ├── synthesis/         # cross-cutting essays, comparisons
    └── templates/         # page templates
```

## Conventions

- **Slugs:** `YYYY-firstauthor-shorttitle` for sources; lowercase kebab-case for topics/concepts/entities.
- **Wikilinks:** Obsidian-style `[[path/to/page]]` for internal references.
- **Frontmatter:** YAML at the top of every wiki page (see `wiki/templates/`).
- **Log entries:** `## [YYYY-MM-DD] <op> | <title>` — greppable with `grep "^## \[" wiki/log.md`.

See [`CLAUDE.md`](./CLAUDE.md) for the full schema.
