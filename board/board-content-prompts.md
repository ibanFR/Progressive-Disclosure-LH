# Session Prompts

Verbatim record of the user's prompts in this session.

1. can you search for Miro board Progressive Disclosure LH?

2. read content and output to @board/board-content.md

3. I want you to read all stickies for frame 10 and output them verbatim in Agent instructions (team knowledge)

4. read the content of these slides https://miro.com/app/board/uXjVH8WrRpA=/?moveToWidget=3458764678059073153&cot=14 and output them into @board/board-slides.md

5. read this Miro board https://miro.com/app/board/uXjVH8WrRpA=/. This is a Samman Learning Hour created by Emily Bache. Use @site/index.md as structure to write the Learning Hour's documentation site.

## Output produced this session

- Wrote [`board/board-slides.md`](./board-slides.md) — the full Progressive Disclosure deck (Learning Hour #GLH053 by Emily Bache) transcribed across 12 frames into 11 logical slides: title/objectives, Connect, The Agent Loop, Context Window, Advice for AGENTS.md, Progressive Disclosure (manual), Skills (automated), Demo, Exercise, and two Conclusions frames. Near-duplicate frames were merged.

> **⚠️ Signpost — Miro tools return AI-generated summaries.** The `mcp__miro__context_get` / `context_explore` tools do **not** return verbatim slide text; they return AI-generated *summaries* of each frame. Consequently, wording in `board-slides.md` (bullet phrasing, quotes, and the design-guideline figures) is **paraphrased**, not exact. For verbatim text, the raw item-level data for a specific frame must be pulled instead.

## Follow-up session

6. create a Samman Learning Hour based on this facilitated session by Emily Bache: https://miro.com/app/board/uXjVPHh12To=/. output to @site/index.md

7. preview the site locally

### Output produced

- Rewrote [`site/index.md`](../site/index.md) — the Progressive Disclosure Learning Hour written into the Samman four-phase structure (Learning Goals, Connect, Concept, Concrete Practice, Conclusions), sourced from the Miro board frames.
- Fixed the local Jekyll build (pinned `public_suffix < 7` for Ruby 3.1, rebuilt native gems) and served the site at http://localhost:4000/.

**Rating: Not good.**

## Follow-up session (2026-07-18)

8. update @site/index.md to use markdown links []() format

9. keep references at the bottom anyway

10. commit this

11. Read @site/index.md . this is a template for a Samman Learning Hour. Use the template for writting a Learning Hour based on this facilitated session in Miro: https://miro.com/app/board/uXjVHPh12To=/. Read all resources on the template as context.

12. add the prompts of this session to @board/board-content-prompts.md

### Output produced

- Converted the reference-style links in [`site/index.md`](../site/index.md) to inline `[]()` markdown links while keeping the reference definitions at the bottom of the file; committed as `31decca`.
- Rewrote [`site/index.md`](../site/index.md) as the full **Progressive Disclosure** Learning Hour (#GLH053 by Emily Bache), mapping the Miro board frames onto the Samman four-phase template (Learning Goals, Connect, Concept, Concrete Practice, Conclusions). Read the template's linked Samman Coaching resources (Learning Hour definition + the four activity pages) as context, and pulled frame content from the board via the Miro MCP tools.

## Follow-up session (2026-07-18)

13. write a facilitation guide for this Miro board: https://miro.com/app/board/uXjVHPh12To=/. create a section for each frame. Use the frame title as heading. output to @site/facilitation-guide.md

14. document your prompt in @board/board-content-prompts.md

### Output produced

- Wrote [`site/facilitation-guide.md`](../site/facilitation-guide.md) — a frame-by-frame facilitation guide for the **Progressive Disclosure** Learning Hour (#GLH053 by Emily Bache), one section per Miro frame using the frame title as the H2 heading. Sections are ordered by the board's left-to-right layout (title → Connect → Design advice → two Agent Loop frames → Context Window → Demo → Progressive Disclosure → Exercise → Skills → Conclusions → Feedback), each with phase, suggested timing, prompts, how-to-run steps, and facilitator notes. Frame content pulled via the Miro MCP tools (`context_explore` + `context_get`); the Feedback frame is treated as a facilitator prep resource rather than a live slide. Added as a `just-the-docs` page with `nav_order: 2` and an auto-generated TOC.
