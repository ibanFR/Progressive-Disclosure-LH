# `docs/` — nWave documentation artifacts

This directory holds **nWave artifacts**: the intermediate and audit outputs produced by
the `/nw-document` skill (the DIVIO/Diataxis documentation pipeline). These files are the
*evidence trail* behind the published documentation — they are **not** the published pages
themselves.

> **Published docs live under [`site/`](../site/)**, not here. The reader-facing How-to and
> Reference pages are the Jekyll pages in `site/how-to/` and `site/reference/`. This
> `docs/` tree is deliberately kept out of `site/` so Jekyll does not copy these artifacts
> into the built `_site/` output.

## What the `/nw-document` pipeline produces

`/nw-document` runs a four-phase pipeline with a peer-review gate at each stage:

```
Phase 1   Research            (nw-researcher)              → docs/research/*.md
Phase 1.5 Research Review     (nw-researcher-reviewer)     → review block appended
Phase 2   Documentation       (nw-documentarist)           → site/**/*.md (+ validation)
Phase 2.5 Documentation Review(nw-documentarist-reviewer)  → review block appended
Phase 3   Handoff
```

## Contents

| Path | Kind | Description |
|------|------|-------------|
| `research/` | Research artifact | Cited, source-verified research feeding the docs. Trusted sources only, cross-referenced, with knowledge gaps and conflicts documented. Ends with an appended **Research Review** YAML block (verdict). |
| `howto/*.validation.yaml` | Validation report | DIVIO classification, type-purity score, collapse detection, six-characteristic quality scores, and a verdict for the matching `site/how-to/` page. Includes the appended **Doc Review** block. |
| `reference/*.validation.yaml` | Validation report | Same, for the matching `site/reference/` page. |

Current artifacts (topic: **YouTube MCP server**):

- `research/youtube-mcp-server-for-howto-reference-doc.md`
- `howto/youtube-mcp-server-guide.md.validation.yaml` → validates `site/how-to/youtube-mcp-server-guide.md`
- `reference/youtube-mcp-server-reference.md.validation.yaml` → validates `site/reference/youtube-mcp-server-reference.md`

## Naming convention

- Research: `docs/research/{topic-kebab-case}-for-{type}-doc.md`
- Validation: `docs/{type-dir}/{published-filename}.validation.yaml` (mirrors the `site/`
  page it audits).

## Editing note

These are **generated audit artifacts**. Regenerate them by re-running `/nw-document` for
the topic rather than hand-editing, so the research → validation → published-doc chain
stays consistent. The published pages a reader should edit live in `site/`.
