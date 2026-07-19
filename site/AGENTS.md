This is the **published** Jekyll site (theme: `just-the-docs`) for the Progressive Disclosure Learning Hour. These are reader-facing pages, deployed to GitHub Pages via `.github/workflows/pages.yml`.

Pages follow the DIVIO/Diataxis model — keep each page to one type and do not mix them:

* `how-to/` — task-oriented guides for a user with a goal.
* `reference/` — dry, exhaustive, lookup-oriented facts.
* `explanation/` — understanding-oriented discussion and background.

Regenerate docs with the `/nw-document` pipeline rather than hand-editing where a matching artifact exists.

The pipeline's research + validation reports live under the repo-root `docs/`, not here — `docs/` is kept out of Jekyll's build.

Preview locally: `./start_local_server.sh` → http://localhost:4000
