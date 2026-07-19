# Documentation Session Prompts

A verbatim record of the user prompts from the documentation session, in order.

1. I want to document the new youtube mcp server, including github repository, installation instructions, api key, and sample prompts. What type of document should I write? Use the nw-documentarist agent

   Claude asked two follow-up questions; user answers:
   - **How should I structure the YouTube MCP docs?** → Two documents (split): separate How-to guide + Reference, cross-linked.
   - **Where should the docs live?** → `site/` (the learning-hour site directory).

2. create a new branch from main. commit changes

3. preview site locally

4. push it and open a PR

5. stop the server

6. can you rename @site/how-to/youtube-mcp-server.md to youtube-mcp-server-guide.md. Do de same with reference file.

7. commit and push it

8. start local server

9. amend last commit, rename @site/reference/youtube-mcp-server-guide.md to youtube-mcp-server-reference.md

10. reword it for accuracy

11. /nw-document "install youtube mcp server"

    Claude asked two follow-up questions (a how-to for this already existed); user answers:
    - **How should /nw-document proceed?** → Generate fresh doc: author a brand-new how-to from scratch via the full pipeline.
    - **Where should output land, given this repo uses `site/` not `docs/`?** → `docs/` (the skill's default paths).

12. create new document in @harness/ named documentation-prompts.md. output the prompts of this session. note generated output in @docs/

## Generated output in `docs/`

The `/nw-document` command (prompt 11) ran a four-phase research → review → author → review
pipeline (agents: nw-researcher, nw-researcher-reviewer, nw-documentarist,
nw-documentarist-reviewer). Both review gates returned APPROVED on the first iteration. It
produced the following files under `docs/`:

- `docs/research/install-youtube-mcp-server-for-howto-doc.md` — cited research (primary
  sources, avg reputation 0.97; Research Review APPROVED, score 0.928).
- `docs/howto/install-youtube-mcp-server.md` — DIVIO HOW-TO guide (type purity 0.94;
  Doc Review APPROVED, score 9.4/10).
- `docs/howto/install-youtube-mcp-server.md.validation.yaml` — validation report
  (classification, purity, collapse detection, readability/style/link/spelling checks).

Note: these `docs/` files are distinct from the published Jekyll site pages under `site/`
(`site/how-to/youtube-mcp-server-guide.md`, `site/reference/youtube-mcp-server-reference.md`),
which cover the same topic and were created in prompts 1–10.

---

# Documentation Session Prompts — Session 2 (2026-07-19)

A verbatim record of the user prompts from the second documentation session, in order.

1. `/nw-document document the new youtube mcp server, including github repository, installation instructions, api key, and sample prompts.`

   Claude found the docs already existed and asked one follow-up; user answer:
   - **The docs already exist and cover all four items — what do you want me to do?** → Regenerate via pipeline (run the full four-phase pipeline, overwriting the existing docs).

2. fix notes 1-2

   (Refers to two non-blocking notes Claude raised at handoff: (1) append the reference
   reviewer's YAML block to the reference `.validation.yaml`; (2) align the fallback env
   var naming between the how-to and reference docs.)

3. `/nw-buddy why did the yaml files where generated in the site folder instead of /docs?`

4. `/nw-buddy leaving validation artifacts in the site folder may have an impact with jekyll static site generation. does it not?`

5. document the prompts of this session to @../harness/documentation-prompts.md

## Generated / modified output — Session 2

The `/nw-document` command (prompt 1) re-ran the four-phase research → review → author →
review pipeline (agents: nw-researcher, nw-researcher-reviewer, nw-documentarist ×2,
nw-documentarist-reviewer ×2). The research gate needed one revision cycle
(NEEDS_REVISION → APPROVED); both documentation gates returned APPROVED on the first
iteration. Files:

- `docs/research/youtube-mcp-server-for-howto-reference-doc.md` — cited research feeding
  both docs (8 trusted sources; Research Review APPROVED at iteration 2). Surfaced a new
  verified fact: the server supports optional `YOUTUBE_API_KEY2`/`YOUTUBE_API_KEY3`
  fallback keys with quota-exhaustion retry.
- `site/how-to/youtube-mcp-server-guide.md` — regenerated DIVIO HOW-TO (type purity 95%;
  Doc Review APPROVED). Overwrote the existing file.
- `site/reference/youtube-mcp-server-reference.md` — regenerated DIVIO REFERENCE
  (type purity 95%, overall 9.08/10; Doc Review APPROVED). Overwrote the existing file.
- `site/how-to/youtube-mcp-server-guide.md.validation.yaml` and
  `site/reference/youtube-mcp-server-reference.md.validation.yaml` — validation reports.

Prompt 2 appended the reference reviewer's review block to the reference `.validation.yaml`
and aligned the fallback env var naming in the how-to (`YOUTUBE_API_TOKEN_2/3` shell vars
→ `YOUTUBE_API_KEY2/3` server slots).

Prompts 3–4 (nw-buddy Q&A) surfaced a known issue, not yet fixed as of this record: the
two `.validation.yaml` files sit under `site/`, and Jekyll copies them verbatim into the
built `_site/` output (confirmed in `_site/how-to/` and `_site/reference/`), so they would
be publicly served on the GitHub Pages site. Recommended remediation (pending user
decision): move the validation artifacts out of `site/` into the `docs/` tree.

---

# Documentation Session Prompts — Session 3 (2026-07-19)

A verbatim record of the user prompts from the third documentation session, in order.

1. `/nw-document youtube-mcp-server-for-howto-reference`

   Claude found the how-to + reference docs already existed and were committed, and asked
   one follow-up; user answer:
   - **The docs already exist and are committed — how should this /nw-document run proceed?**
     → **Regenerate both (overwrite)** — run the full four-phase pipeline and overwrite both
     `site/` docs, with validation `.yaml` written under `docs/` (not `site/`) to avoid the
     Jekyll-pollution issue flagged in Session 2.

2. document the prompts of this session to @../harness/documentation-prompts.md

## Generated / modified output — Session 3

The `/nw-document` command (prompt 1) re-ran the four-phase research → review → author →
review pipeline (agents: nw-researcher, nw-researcher-reviewer, nw-documentarist,
nw-documentarist-reviewer). The research gate needed one revision cycle
(NEEDS_REVISION → APPROVED); both documentation reviews returned APPROVED on the first
iteration. Files:

- `docs/research/youtube-mcp-server-for-howto-reference-doc.md` — regenerated cited research
  (overall 0.89; Research Review APPROVED). This run **closed all four gaps** the prior run
  left open: YouTube Data API v3 quota costs (`search.list`=100 units, list ops=1,
  `captions.list`=50 but unused by this server, 10,000 units/day default); the **Node.js
  floor** (the package declares no `engines.node`; Node ≥18 is imposed transitively by
  `googleapis@^173` and `@modelcontextprotocol/sdk@^1.1.1`); the **extended tool filter
  params** (now source-confirmed at the `src/server.ts` schema layer, with the
  `functions/*.ts` vs `server.ts` conflict documented); and re-verified v1.0.2 / 2026-07-16
  / MIT.
- `site/how-to/youtube-mcp-server-guide.md` — regenerated DIVIO HOW-TO (type purity 90%,
  measured 97.5%; Doc Review APPROVED). Overwrote the existing file. Features the env-var
  gotcha (export `YOUTUBE_API_TOKEN`, not `YOUTUBE_API_KEY`) prominently.
- `site/reference/youtube-mcp-server-reference.md` — regenerated DIVIO REFERENCE (type
  purity 95%, measured 99%; Doc Review APPROVED). Overwrote the existing file.
- `docs/howto/youtube-mcp-server-guide.md.validation.yaml` and
  `docs/reference/youtube-mcp-server-reference.md.validation.yaml` — validation reports,
  each with the peer-reviewer's YAML block appended.

The Session-2 Jekyll issue is **resolved this run**: the two `.validation.yaml` reports were
written under the `docs/` tree, not `site/`, so they are no longer copied into the built
`_site/` output. Verified no `*.validation.yaml` exists under `site/`.

During the research-review cycle, the reviewer (read-only) returned NEEDS_REVISION for a
single off-allowlist source (`api.github.com`, informational only); the orchestrator applied
the fix directly — removed the source from the Source Analysis table and Full Citations,
renumbered citations `[7–9]→[6–8]`, and updated the source counts `9→8`.

Changes were left uncommitted on branch `add-youtube-mcp-docs` (3 modified files + 2 new
`docs/` subdirectories) pending user direction.
