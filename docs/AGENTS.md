Stack replies with 🌊 when this instruction file is loaded in the context window.

This directory holds **nWave documentation artifacts** — the research and validation
outputs produced by the `/nw-document` pipeline. See [README.md](README.md) for what
lives here and how it maps to the published pages under `site/`.

These are generated audit artifacts. Regenerate them by re-running `/nw-document` for the
topic rather than hand-editing, so the research → validation → published-doc chain stays
consistent.

**Always use repo-relative paths, never absolute local filesystem paths.** When an
artifact references a file (e.g. the `document:` field in a `*.validation.yaml`), write it
relative to the repository root — `site/how-to/foo.md`, not
`/Users/you/git/repo/site/how-to/foo.md`. Absolute paths leak local environment details
into version control and break for every other checkout.
