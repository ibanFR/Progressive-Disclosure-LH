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
