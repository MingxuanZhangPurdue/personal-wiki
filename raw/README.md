# raw/

**Immutable source layer.** Original documents live here. Never edit them after adding.

## Layout

```
raw/
  papers/<YYYY-firstauthor-shorttitle>/
    paper.pdf
    source.md          # citation + metadata
    assets/            # optional figures, supplementary files
  blogs/<YYYY-author-shorttitle>/
    clip.md            # markdown capture (primary, LLM-readable)
    original.html      # single-file HTML backup (optional but recommended)
    source.md          # URL + archive link + metadata
    assets/            # optional images, screenshots
```

## Adding a paper

1. Create folder: `raw/papers/2023-yao-react/`
2. Drop the PDF in as `paper.pdf`
3. Create `source.md` (copy from `wiki/templates/raw-source.md`) and fill citation/URL/date
4. Ask Claude to ingest it

## Adding a blog / webpage / HTML article

**Always save locally. Always record the URL + a Wayback snapshot.** Links rot.

1. Create folder: `raw/blogs/2024-karpathy-llm-os/`
2. Save the page in **at least one** of these formats (markdown preferred):
   - **Markdown** as `clip.md` — use [Obsidian Web Clipper](https://obsidian.md/clipper), a browser "Save as Markdown" extension, or `pandoc page.html -o clip.md`. This is what Claude reads.
   - **Single-file HTML** as `original.html` — use [`monolith`](https://github.com/Y2Z/monolith) (`monolith https://... -o original.html`) or the browser's "Save Page As → Webpage, Single File". Keeps images/CSS inlined.
3. Submit the URL to the Wayback Machine: `https://web.archive.org/save/<url>` — copy the resulting archive URL.
4. Create `source.md` (copy from `wiki/templates/raw-source.md`) and fill in URL, archive URL, date.
5. Ask Claude to ingest it.

### Which format to save?

| You have... | Save as |
|---|---|
| Blog post, article, doc page | `clip.md` (primary) + `source.md` |
| Heavily-formatted page where layout matters | `clip.md` + `original.html` + `source.md` |
| Page with key images/diagrams | `clip.md` + `assets/*.png` + `source.md` |
| Talk / video | `source.md` with URL; optionally `transcript.md` |
| Podcast | `source.md` with URL; optionally `transcript.md` |

### Why save locally?

- Pages disappear, get paywalled, or get rewritten.
- Markdown is diffable, greppable, and the LLM reads it natively in one pass — HTML is noisy.
- Wayback is third-party insurance, but you don't want to depend on it.

## Adding other sources

Talks, podcasts, book chapters, internal docs all work the same way:
- Make a folder with a slug.
- Put whatever artifact you have inside (PDF, transcript, audio link).
- Always create a `source.md`.
