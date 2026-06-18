# Wiki Log

> Append-only chronological record of wiki activity.
> Entry prefix: `## [YYYY-MM-DD] <op> | <title>` where `<op>` is `ingest`, `query`, or `lint`.
> Greppable: `grep "^## \[" wiki/log.md | tail -10`

---

## [2026-06-17] init | Wiki scaffolded

Created repository structure per [`llm-wiki.md`](../llm-wiki.md):
- `CLAUDE.md` schema with ingest/query/lint workflows
- `raw/` (papers/, blogs/) — immutable source layer
- `wiki/` (sources/, topics/, concepts/, entities/, synthesis/, templates/) — LLM-owned layer
- `wiki/index.md` and `wiki/log.md` initialized empty
- Page templates in `wiki/templates/`

Ready to ingest first source.

## [2026-06-17] seed | topic stubs

Created empty topic hub pages for the human's current areas of interest:
- [[topics/agentic-rl]]
- [[topics/self-evolving-agents]]
- [[topics/agent-memory]]
- [[topics/rag]]
- [[topics/agent-harness]]
- [[topics/sandbox]]
- [[topics/evaluation]]
- [[topics/benchmarks]]

Updated [[index]] topic list. All stubs have 0 sources; they will fill in as ingests happen.
