# Research: Progressive Disclosure for Coding Agents & `AGENTS.md` (EXPLANATION doc source)

**Date**: 2026-07-20 | **Researcher**: nw-researcher (Nova) | **Confidence**: High (core claims) / Medium (project-specific figures) | **Sources**: 7 cited + in-repo ground truth

> Purpose: a lift-ready research artifact for a Diataxis **EXPLANATION** page. It is understanding-oriented — it explains *why* progressive disclosure matters for agents, where the idea comes from, and the trade-offs — not step-by-step instructions. The canonical framing is set by `site/index.md` (the Learning Hour session plan) and the published page `site/explanation/progressive-disclosure.md`; this artifact deepens, sources, and stress-tests that framing. Describe the pattern **literally**: `AGENTS.md` stays small and holds triggers/pointers; knowledge documents hold the full detail and load on demand. (No hub-and-spoke or other metaphor — the published page deliberately dropped it.)

---

## 1. Executive Summary

Progressive disclosure is a decades-old design principle — popularized by usability engineer Jakob Nielsen and rooted in 1970s–80s HCI and John Carroll's "minimalist" instructional design — that says: show only what is needed now, and defer everything else until it is actually requested. Applied to coding agents, it means keeping the always-loaded `AGENTS.md` short (holding *triggers and pointers*, not full detail) and letting the agent pull in knowledge documents *on demand*. This matters because of a hard mechanical constraint: on every iteration of the agent loop, the harness re-injects `AGENTS.md` + system prompt + full history into a **finite** context window, and model recall actually *degrades* as that window fills ("context rot" / a limited "attention budget").

Anthropic's own guidance reframes the whole discipline as **context engineering** and endorses a "just-in-time" loading strategy; Agent Skills push the idea one level further by disclosing even the *pointers* progressively (only a `name` + `description` are indexed at startup — roughly 100 tokens per Skill — until a skill is selected). The pattern is structural, not metaphorical: `AGENTS.md` and the harness are always loaded and hold short pointers; each knowledge document contains the full story and enters the window only when its trigger fires.

The main tension is discoverability vs. minimalism: on-demand loading only works if the agent reliably knows *when* to load a document — which is why the project pairs it with the emoji-verification trick to *observe* what was actually read, then iterates on trigger wording.

---

## 2. Key Claims Table

| # | Claim | Sources | Confidence | Notes |
|---|-------|---------|-----------|-------|
| C1 | Progressive disclosure = "show a few important options first; offer the larger specialized set on request," to defer advanced/rarely-used features and make apps easier to learn and less error-prone. | NN/g (Nielsen) [S1]; IxDF [S2] | **High** | Nielsen's definition is the canonical primary source. |
| C2 | The principle originates with Jakob Nielsen and 1970s–80s HCI; the underlying strategy is "more than 30 years old." | NN/g [S1]; IxDF [S2] | **High** | NN/g (2006) dates the strategy to "more than 30 years old" (i.e. mid-1970s); no exact "coined in year X" in the primary article — see Open Questions. |
| C3 | Conceptual ancestor in instructional design: John Carroll's "minimalism" / Minimal Manual (1987–88) & "training wheels" interface — severely reduce explicit instruction, support guided exploration, defer advanced functions. | InstructionalDesign.org [S7]; academic reviews [S7a] | **High** | Independent lineage that reinforces the "minimal, on-demand" framing for documentation. |
| C4 | For coding agents, `AGENTS.md` should stay short and hold triggers/pointers; whole knowledge documents load only when needed. | Project ground truth `site/index.md`, `site/explanation/progressive-disclosure.md`; Anthropic Skills [S4]; Anthropic context engineering [S5] | **High** | Directly corroborated by Anthropic's just-in-time + Skills architecture. Stated literally — no metaphor. |
| C5 | On **every** loop iteration the harness re-injects `AGENTS.md` + system prompt + full history into the LLM; the context window is a finite shared stack. | Anthropic context engineering [S5]; project ground truth | **High** | Anthropic: context is "a finite resource with diminishing marginal returns"; the per-iteration re-injection is standard agent-loop mechanics and matches the project framing (not a verbatim Anthropic quote — see Open Questions). |
| C6 | Model recall degrades as tokens grow ("context rot"); LLMs have a finite "attention budget" that each new token depletes. | Anthropic context engineering [S5] | **High** | Anthropic states this explicitly and generalizes it across models. |
| C7 | "100–150 lines is optimal for `AGENTS.md`" (with a handful of focused reference docs); best files ≈ **10–15%** improvement across metrics in mid-size modules; a good one is "a quality jump equivalent to upgrading from Haiku to Opus." | Augment Code blog [S3] | **Medium** | **Single-source, vendor blog.** Numbers come from Augment's internal eval (AuggieBench). Attribute explicitly; do not present as independent consensus. |
| C8 | Past the optimal length — or when surrounding module docs are excessive (e.g. ~500K characters total) — performance degrades (one task: completeness dropped 25%). | Augment Code blog [S3] | **Low/Medium** | Same vendor lineage as C7. The earlier artifact's specific "20–23% inference-cost" figure was **not** confirmed on re-verification; the current post frames this as performance degradation, not a fixed cost percentage. Treat as corroborating detail, not independent evidence. |
| C9 | Agent Skills load in 3 levels: (1) `name`+`description` metadata indexed at startup (~100 tokens/skill); (2) full SKILL.md loaded when triggered (<5k tokens); (3) referenced files/scripts read/run on demand (0 tokens until accessed). | Anthropic Skills [S4]; Platform docs [S4a] | **High** | Platform docs now give an explicit per-level table. Note: `site/index.md` still says "first 3 lines"; Anthropic's canonical description is the YAML `name` + `description` frontmatter — see Open Questions. |
| C10 | Context engineering (curating the optimal token set at inference) is the discipline that supersedes prompt engineering; "just-in-time" loading uses lightweight identifiers (paths, queries, links) resolved at runtime, vs. embedding-based pre-inference retrieval. | Anthropic context engineering [S5] | **High** | The umbrella concept the explanation connects to. |
| C11 | Diataxis: an *explanation* is understanding-oriented (why/background/connections), distinct from tutorials/how-tos/reference. | Diataxis [S6]; repo `site/AGENTS.md` | **High** | Governs the doc's *shape*, per the repo's own conventions. |

---

## 3. Thematic Sections

### 3.1 Origins & Definition — where "progressive disclosure" comes from

**Canonical definition (Nielsen).** Jakob Nielsen, co-founder of the Nielsen Norman Group, frames progressive disclosure as the resolution of a standing conflict between users' desire for *power* and their desire for *simplicity*:

> "Initially, show users only a few of the most important options. Offer a larger set of specialized options upon request." — NN/g [S1]

Its explicit purpose is to defer advanced or rarely used features to a secondary screen, making applications easier to learn and less error-prone [S1]. The mechanism is cognitive-load management: hiding advanced settings helps novice users avoid mistakes and saves the time they would spend contemplating features they don't need [S1]. Notably, Nielsen cites research that hiding features does *not* harm comprehension — people understand a system better when you help them prioritize features and spend more time on the most important ones; concerns about limiting users' mental models are unfounded [S1]. The NN/g article is dated 3 December 2006 and describes the strategy as "more than 30 years old" [S1].

**Instructional-design lineage (Carroll's minimalism).** A parallel and reinforcing root is John M. Carroll's *minimalism*, formalized in *The Nurnberg Funnel* and the "Minimal Manual" (1987–88). Its hallmarks — brevity/conciseness, focus on real tasks, support for error recognition and recovery, and "guided exploration" — are the documentation analogue of progressive disclosure [S7]. Carroll's "training wheels" interface deferred access to advanced/error-prone functions for beginners, reducing error-recovery time [S7]. This lineage matters for the explanation because `AGENTS.md` *is* documentation-for-an-agent: the argument "keep it minimal, let the reader/agent pull detail on demand" is exactly Carroll's thesis, forty years on.

**Transfer to LLM context.** The concept transfers cleanly because both a human UI and an LLM context window are attention-constrained surfaces. In UI, the scarce resource is human working memory and screen space; in agents, it is the model's finite context window and "attention budget" (§3.2). Same principle, new substrate.

### 3.2 Why it matters for agents — the mechanism

Three linked ideas (as framed in `site/index.md`) motivate progressive disclosure, and external sources back the load-bearing ones:

1. **The agent loop is iterative and re-injects context every pass.** An agent orchestrates repeated LLM calls interleaved with tool calls. On *every* iteration the harness sends the system prompt + `AGENTS.md` + the full conversation history back into the model. Nothing loaded permanently is "paid for once" — it is paid for on every pass. (This per-iteration re-injection is standard agent-loop mechanics; the project asserts it, and it is consistent with Anthropic's description of managing "the entire context state ... across multi-turn agent interactions" [S5].)

2. **The context window is a finite, shared stack.** Anthropic states it plainly: context "must be treated as a finite resource with diminishing marginal returns," and "LLMs have an 'attention budget' ... Every new token introduced depletes this budget" [S5]. Every line permanently occupying `AGENTS.md` is a line unavailable for the actual task.

3. **More context is not free — recall degrades ("context rot").** As the number of tokens in the context window increases, the model's ability to accurately recall information from that context decreases [S5]. So a bloated `AGENTS.md` is doubly costly: it consumes budget *and* can dilute the model's attention on what matters.

**The pay-off.** Anthropic's prescribed remedy is exactly progressive disclosure under a different name — the **"just-in-time" approach**, where agents "maintain lightweight identifiers (file paths, stored queries, web links, etc.) and use these references to dynamically load data into context at runtime using tools" [S5]. That is precisely the structure the project uses: `AGENTS.md` holds the lightweight pointers; the knowledge documents hold the content and are loaded only at the moment each is needed.

### 3.3 The core model & rules (as the project frames it)

**Pointers, loaded on demand.** `AGENTS.md` and the harness are always loaded and stay small, holding only triggers and pointers; knowledge documents (e.g. Test Design Advice, Class Design Advice, Commit Message Advice) contain the full detail and are read on demand. Two rules (`site/index.md`, `site/explanation/progressive-disclosure.md`):

- **Tell the agent *when* to read each document** — the trigger lives in `AGENTS.md`; the content lives elsewhere. A one-sentence pointer tells the agent *why* and *when* to load the whole document.
- **Whole documents load only when needed** — nothing enters the window until its moment.

This resolves the "Connect" tension surfaced in the session: the list of things people *want* in `AGENTS.md` is enormous (coding standards, commit conventions, architectural constraints, refactoring patterns, project context, repo layout, build/TDD instructions), yet the file must stay short. Progressive disclosure dissolves the contradiction — the file stays short by holding pointers/triggers, not detail → more spare capacity → better, cheaper performance. It does not ask teams to *cut* content; it asks them to *defer* it.

**Design guidance (attribute carefully).** The project cites the Augment Code blog: 100–150 lines optimal (with a handful of focused reference docs), best files ≈ 10–15% uplift across metrics in mid-size modules, "a quality jump equivalent to upgrading from Haiku to Opus," with performance degrading past the optimal length or when surrounding docs balloon [S3]. These are **single-source, vendor-internal** figures (AuggieBench eval) — genuinely useful as a concrete anchor, but should be presented as "Augment Code reports…," not as established, independently-replicated fact. See §5.

**Verification trick.** Because on-demand loading is only as good as the agent's decision to load, the project has each knowledge doc emit an **emoji** when read, so a facilitator can *see* in the transcript which docs were actually pulled. This is a practical response to the central failure mode (§3.4).

### 3.4 Trade-offs, tensions & alternatives

| Tension | On-demand / progressive disclosure | Alternative | Trade-off |
|---------|-----------------------------------|-------------|-----------|
| Eager vs. lazy | Load only when triggered; window stays free | Eager-load everything into `AGENTS.md` | Eager guarantees availability but burns budget every iteration and invites context rot [S5]. |
| Explicit triggers vs. retrieval | Human-written triggers in `AGENTS.md` point to docs | Embedding/RAG retrieval at inference | Anthropic contrasts "just-in-time" (runtime references) with "embedding-based pre-inference retrieval" [S5]; RAG adds infra + relevance risk + latency, triggers add authoring burden but stay explicit and deterministic. |
| Discoverability vs. minimalism | Minimal file keeps context lean | Rich file aids discovery | **Core risk: the agent fails to load a needed doc** because the trigger was unclear — hence the emoji check and iterating on trigger wording. |
| Determinism vs. autonomy | Agent decides what to read | Pre-wired mandatory reads | Autonomy scales to many docs but is probabilistic; mandatory reads are reliable but reintroduce bloat. |

**Named failure mode:** under-disclosure — the agent skips a needed document or routes to the wrong one. The mitigation is not "load everything" (that defeats the purpose) but *better triggers + observability* (emoji sensors) and iteration.

### 3.5 Connections — Skills, context engineering, Diataxis

- **Agent Skills = disclosure one step further.** Skills need not be named in `AGENTS.md` at all; the agent discovers them in a known folder. Anthropic's model loads in three levels [S4a]:
  - **Level 1 — Metadata (always loaded):** only each skill's `name` + `description` YAML frontmatter is pre-loaded into the system prompt at startup, roughly ~100 tokens per skill — "just enough ... to know when each skill should be used."
  - **Level 2 — Instructions (loaded when triggered):** the full `SKILL.md` body (under ~5k tokens) enters context only when the agent judges the skill relevant.
  - **Level 3+ — Resources & code (loaded as needed):** bundled reference files load into context only when read; scripts run via bash and only their output (not their code) consumes tokens.

  Anthropic's punchline: because only metadata is pre-loaded, "the amount of context that can be bundled into a skill is effectively unbounded" [S4] — there is "no practical limit on bundled content" [S4a]. This is progressive disclosure applied to the *pointers themselves*. (Nuance: `site/index.md` still says "first 3 lines"; Anthropic's canonical description is the YAML frontmatter `name` + `description` — see Open Questions. The published explanation page already uses the precise "metadata (name and description)" framing.)

- **Context engineering** is the umbrella discipline: "the set of strategies for curating and maintaining the optimal set of tokens (information) during LLM inference" [S5], succeeding prompt engineering as agents run long, multi-turn tasks. Progressive disclosure is one concrete tactic within it (alongside compaction, sub-agents, structured note-taking).

- **Diataxis** governs the *doc's own shape*: an explanation is understanding-oriented — background, reasons, connections, trade-offs — deliberately *not* a how-to [S6]. The repo's `site/AGENTS.md` enforces "one Diataxis type per page, never mixed," so this page should resist drifting into step-by-step instructions.

### 3.6 Suggested "Further reading" (with URLs)

- Nielsen, *Progressive Disclosure* — https://www.nngroup.com/articles/progressive-disclosure/
- Anthropic, *Effective context engineering for AI agents* — https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents
- Anthropic, *Equipping agents for the real world with Agent Skills* — https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills
- Anthropic/Claude Platform, *Agent Skills overview* — https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview
- Augment Code, *A good AGENTS.md is a model upgrade…* — https://www.augmentcode.com/blog/how-to-write-good-agents-dot-md-files
- Carroll's minimalism (summary) — https://www.instructionaldesign.org/theories/minimalism/
- Diataxis, *Explanation* — https://diataxis.fr/explanation/
- Interaction Design Foundation, *Progressive Disclosure* — https://www.interaction-design.org/literature/topics/progressive-disclosure

---

## 4. Strongest citable external URLs (for a References section)

1. **Nielsen Norman Group — "Progressive Disclosure" (Jakob Nielsen, 2006-12-03)** — https://www.nngroup.com/articles/progressive-disclosure/ — *High (1.0). Canonical primary source for the term.*
2. **Anthropic — "Effective context engineering for AI agents" (2025-09-29)** — https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents — *High (1.0). Primary source for finite context, attention budget, context rot, just-in-time loading.*
3. **Anthropic — "Equipping agents for the real world with Agent Skills" (2025-10-16)** — https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills — *High (1.0). Primary source for Skills progressive-disclosure levels.*
4. **Anthropic / Claude Platform Docs — "Agent Skills overview"** — https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview — *High (1.0). Official product docs; explicit 3-level loading table.*
5. **Augment Code — "A good AGENTS.md is a model upgrade…" (2026-04-22, updated 2026-06-18)** — https://www.augmentcode.com/blog/how-to-write-good-agents-dot-md-files — *Medium (0.6). Vendor blog; source of the 100–150 line & 10–15% figures — cite by name, note bias.*
6. **Diataxis — "Explanation"** — https://diataxis.fr/explanation/ — *High (0.9). Authoritative for the documentation framework.*
7. **InstructionalDesign.org — "Minimalism (J. Carroll)"** — https://www.instructionaldesign.org/theories/minimalism/ — *Medium-High (0.7). Secondary but reliable summary of Carroll.*
8. **Interaction Design Foundation — "Progressive Disclosure"** — https://www.interaction-design.org/literature/topics/progressive-disclosure — *Medium-High (0.8). Corroborates NN/g definition & history.*

---

## 5. Open Questions / Uncorroborated Claims

1. **Augment Code figures (C7/C8) are single-source and vendor-internal.** The "100–150 lines," "10–15% uplift," and "Haiku→Opus" numbers all trace to Augment Code's own AuggieBench eval [S3]. No independent replication was found. On re-verification (post updated 2026-06-18), the earlier artifact's specific "20–23% inference-cost" figure could **not** be confirmed; the current post instead frames over-length as *performance degradation* (e.g. completeness dropped 25% when total module docs reached ~500K characters). **Bias flag:** Augment sells a coding-agent product; the post promotes their approach. *Recommendation for the doc:* present as "Augment Code reports…" with a one-line caveat; do not aggregate as consensus, and drop the unconfirmed 20–23% number.

2. **"First 3 lines" of a skill (project) vs. "name + description frontmatter" (Anthropic).** Anthropic's canonical description is that the YAML `name` and `description` metadata are indexed at startup (~100 tokens/skill) [S4][S4a], not literally "the first 3 lines." In practice the frontmatter often *is* the first few lines, so `site/index.md`'s phrasing is a reasonable simplification — but the explanation should prefer the precise "name + description metadata" framing (the published explanation page already does).

3. **Exact coinage year of "progressive disclosure."** The NN/g primary article does not give a single "coined in year X." It states the strategy is "more than 30 years old" (mid-1970s HCI). If the doc wants a date, phrase it as "popularized by Nielsen; roots in 1970s–80s HCI" rather than a hard citation.

4. **Per-iteration re-injection of `AGENTS.md` (C5).** This is standard agent-loop behavior and consistent with Anthropic's "entire context state across multi-turn interactions" framing, but no single source states "`AGENTS.md` is re-sent on every iteration" verbatim. Treat as a well-founded mechanism and project framing, not a quotable Anthropic claim.

5. **`AGENTS.md` as an open cross-vendor standard.** The doc could optionally note that `AGENTS.md` is a shared convention across multiple agent tools (agents.md). Not deeply researched here; low priority for an *explanation* page but a possible enrichment.

---

## 6. Source Analysis

| Source | Domain | Reputation | Type | Access | Cross-verified |
|--------|--------|-----------|------|--------|----------------|
| S1 NN/g Progressive Disclosure | nngroup.com | High (1.0) | Industry-authoritative primary | 2026-07-20 | Y (S2) |
| S2 IxDF Progressive Disclosure | interaction-design.org | Med-High (0.8) | Curated reference | 2026-07-20 | Y (S1) |
| S3 Augment Code AGENTS.md | augmentcode.com | Medium (0.6) | Vendor blog (biased) | 2026-07-20 | N (single-source) |
| S4 Anthropic Agent Skills | anthropic.com | High (1.0) | Official engineering | 2026-07-20 | Y (S4a) |
| S4a Claude Platform Skills docs | platform.claude.com | High (1.0) | Official product docs | 2026-07-20 | Y (S4) |
| S5 Anthropic Context Engineering | anthropic.com | High (1.0) | Official engineering | 2026-07-20 | Y (partial, S3) |
| S6 Diataxis Explanation | diataxis.fr | High (0.9) | Framework author | 2026-07-20 | Y (repo AGENTS.md) |
| S7 InstructionalDesign.org Minimalism | instructionaldesign.org | Med-High (0.7) | Secondary academic summary | 2026-07-20 | Y (S7a academic) |

Reputation (7 cited; S4a counted with S4): High: 5 | Medium-High: 2 | Medium: 1 | Avg ≈ 0.87. (High: S1, S4/S4a, S5, S6; Medium-High: S2 IxDF, S7 InstructionalDesign.org; Medium: S3 Augment. Average uses per-source scores S6=0.9 and S7=0.7.)

---

## 7. Full Citations

[S1] Nielsen, J. "Progressive Disclosure." Nielsen Norman Group. 2006-12-03. https://www.nngroup.com/articles/progressive-disclosure/. Accessed 2026-07-20.
[S2] Interaction Design Foundation. "Progressive Disclosure." interaction-design.org. https://www.interaction-design.org/literature/topics/progressive-disclosure. Accessed 2026-07-20.
[S3] Augment Code. "A good AGENTS.md is a model upgrade. A bad one is worse than no docs at all." augmentcode.com/blog. Published 2026-04-22, updated 2026-06-18. https://www.augmentcode.com/blog/how-to-write-good-agents-dot-md-files. Accessed 2026-07-20.
[S4] Anthropic. "Equipping agents for the real world with Agent Skills." anthropic.com/engineering. 2025-10-16. https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills. Accessed 2026-07-20.
[S4a] Anthropic. "Agent Skills — overview." Claude Platform Docs. https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview. Accessed 2026-07-20.
[S5] Anthropic. "Effective context engineering for AI agents." anthropic.com/engineering. 2025-09-29. https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents. Accessed 2026-07-20.
[S6] Procida, D. "Explanation." Diataxis. https://diataxis.fr/explanation/. Accessed 2026-07-20.
[S7] "Minimalism (J. Carroll)." InstructionalDesign.org. https://www.instructionaldesign.org/theories/minimalism/. Accessed 2026-07-20. [S7a] Carroll, J.M. *The Nurnberg Funnel / The Minimal Manual* (1987–88), as summarized in academic reviews.

## 8. Research Metadata
Examined: ~15 candidate sources | Cited: 7 | Cross-refs: core claims C1–C6, C9–C11 multi-source; C7–C8 single-vendor (flagged) | Confidence: High on origins + mechanism; Medium on project-specific numbers | Output: docs/research/progressive-disclosure-for-explanation-doc.md

**Changed vs. prior version (2026-07-19):**
- **Metaphor removed.** All "hub-and-spoke / hub / spoke" language deleted per the published page's decision. C4 title, §3.2 pay-off, and §3.3 (renamed to "Pointers, loaded on demand") now describe the pattern literally: `AGENTS.md` holds triggers/pointers; knowledge documents hold full detail and load on demand.
- **Source currency re-verified** (all four primary sources re-fetched 2026-07-20). Added publication dates: Anthropic context engineering 2025-09-29; Anthropic Agent Skills 2025-10-16.
- **Augment blog refreshed:** now dated 2026-04-22 (updated 2026-06-18). C7 wording aligned to current post ("10–15% improvements across all metrics in mid-size modules," "a quality jump equivalent to upgrading from Haiku to Opus," "a handful of focused reference documents").
- **C8 corrected:** the prior "~20–23% inference-cost" figure was not confirmed on re-verification and has been replaced with the post's actual framing (performance degradation past optimal length; ~500K-char example; completeness −25%). Flagged in Open Question #1.
- **C9/Skills strengthened:** Platform docs now provide an explicit 3-level loading table (Level 1 metadata ~100 tokens/skill always loaded; Level 2 SKILL.md <5k tokens when triggered; Level 3+ resources/scripts on demand), reinforcing "name + description" over "first 3 lines" (Open Question #2).
</content>
</invoke>
