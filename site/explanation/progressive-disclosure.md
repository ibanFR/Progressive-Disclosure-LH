---
title: Progressive Disclosure
layout: default
parent: Explanation
nav_order: 3
---

# Progressive Disclosure
{: .no_toc }

Progressive disclosure is a design principle that keeps essential information visible while deferring specialized detail until it is actually needed — and when applied to coding agents, it solves a mechanical constraint: the context window is finite, shared, and re-filled on every loop iteration.
{: .fs-6 .fw-300 }

## Table of Contents
{: .no_toc .text-delta }

1. TOC
{:toc}

## What is Progressive Disclosure?

Progressive disclosure is a design principle, rooted in 1970s–80s HCI and popularized by usability engineer Jakob Nielsen, that embodies a simple rule: show only what is needed now, and defer everything else until actually requested. Nielsen frames it as resolving the tension between power and simplicity: "Initially, show users only a few of the most important options. Offer a larger set of specialized options upon request." A parallel lineage comes from John Carroll's minimalist instructional design—the *Minimal Manual* (1987–88)—which applies the same logic to documentation through brevity, real tasks, and guided exploration. Both rest on the same insight: attention is scarce, whether human or artificial.

For coding agents, the principle is straightforward: keep `AGENTS.md` short—holding *triggers and pointers*, not full detail—and let the agent pull knowledge documents *on demand*. The scarce resource is the context window. On every loop iteration, the harness re-injects `AGENTS.md`, system prompt, and conversation history into the LLM, and model recall degrades as tokens accumulate. The remedy is the structure explained below: `AGENTS.md` holds short pointers; each knowledge document loads only when its trigger fires. This is what Anthropic calls "context engineering" and "just-in-time" loading. Agent Skills push the idea one step further, disclosing even the pointers progressively. The mechanics, trade-offs, and how to observe what actually loads—all explained ahead.

## Why It Matters for Coding Agents

Three linked mechanical ideas explain why progressive disclosure matters when you orchestrate an LLM-based coding agent.

### The Agent Loop Is Iterative

An agent is an orchestrator that calls the LLM repeatedly, interleaving tool calls (read a file, run a command, propose code) with reasoning. On *every* iteration—not just the first one—the harness re-injects the system prompt, `AGENTS.md`, and the full conversation history into the LLM. Nothing is "paid for once." A line that sits in `AGENTS.md` is re-sent and re-tokenized on pass one, pass two, pass three, and beyond.

### The Context Window Is Finite and Shared

Anthropic states this plainly: context "must be treated as a finite resource with diminishing marginal returns," and "LLMs have an 'attention budget' … Every new token introduced depletes this budget." The window is a shared stack: system prompt, harness instructions, `AGENTS.md`, your task prompts, the agent's working memory, and spare capacity for the actual work. Every line in `AGENTS.md` is a line unavailable for the task itself. Over many iterations, that adds up.

### More Context Comes With a Cost

As tokens accumulate in the context window, the model's ability to accurately *recall* information from that context decreases. Anthropic calls this "context rot." So a bloated `AGENTS.md` is doubly costly: it consumes the finite budget *and* can actually degrade the model's attention on what matters most.

Put together, these three ideas point to a hard trade-off: include everything people *want* in `AGENTS.md`, and the agent pays a compounding performance cost on every iteration. Defer the detail to on-demand loading, and the agent stays sharp—but only if it can reliably *find* what it needs when it needs it.

## Pointers, Loaded on Demand

Progressive disclosure resolves this trade-off through a structural pattern. `AGENTS.md` and the harness are always loaded; they hold short pointers and triggers. Knowledge documents—Test Design Advice, Class Design Advice, Commit Message Guidelines, and the like—contain the full detail and load only when needed. The model enforces two rules:

1. **Tell the agent *when* to read each document.** The trigger lives in `AGENTS.md`; the content lives elsewhere. A one-sentence pointer tells the agent *why* and *when* to load the whole document.
2. **Whole documents load only when needed.** Nothing enters the context window until the moment it is actually relevant.

This dissolves the tension visible in the Learning Hour: the list of things a team *wants* in `AGENTS.md` is enormous—coding standards, commit conventions, architectural constraints, refactoring patterns, project context, repo layout, build instructions. Progressive disclosure doesn't ask teams to cut content; it asks them to *defer* it. Keep `AGENTS.md` short by holding pointers, not detail. The knowledge documents hold the full story.

The payoff: `AGENTS.md` stays lean, the agent has spare context capacity on every loop, and the model's attention budget goes where it matters.

## Context Engineering: The Larger Frame

Progressive disclosure is one tactic within a larger discipline that Anthropic calls **context engineering**—the set of strategies for curating and maintaining the optimal set of tokens during LLM inference. It succeeds prompt engineering as agents run long, multi-turn tasks. The exact prescription Anthropic endorses is called "just-in-time" loading: agents "maintain lightweight identifiers (file paths, stored queries, web links, etc.) and use these references to dynamically load data into context at runtime using tools."

That is exactly this pattern: lightweight pointers in the always-loaded file, full content loaded on demand. Progressive disclosure, in Anthropic's framing, is context engineering made concrete.

## Trade-Offs and Tensions

Like any design principle, progressive disclosure involves trade-offs. Understanding them helps explain when the pattern works well and where it strains.

**Eager loading vs. lazy loading.** Progressive disclosure bets on lazy loading: load only when triggered. The alternative is eager loading—put everything important into `AGENTS.md` and pay the cost on every iteration. Eager loading guarantees availability but invites context rot. Lazy loading keeps the window lean but shifts the burden: the agent must *know* when to load a document.

**Explicit triggers vs. retrieval.** Progressive disclosure relies on human-written triggers in `AGENTS.md` pointing to documents. An alternative is embedding-based retrieval (RAG), where the system searches a knowledge base at inference time. Triggers add authoring burden but are explicit and deterministic. Retrieval is automatic but introduces infrastructure, relevance risk, and adds latency. Anthropic distinguishes the two: "just-in-time" loading (triggers) vs. "embedding-based pre-inference retrieval." Both can work; they trade off simplicity against automation.

**Discoverability vs. minimalism.** A lean `AGENTS.md` is cleaner and cheaper. But a team that knows `AGENTS.md` is short may not *look* for a trigger to something they need. By contrast, a fat `AGENTS.md` makes everything visible—but at the cost of context bloat and the very problem we are trying to solve. The practical answer is better triggers and observability. The emoji verification trick addresses this: each knowledge document emits an emoji when read, so a facilitator can *see* in the output which documents were actually loaded. If the agent skipped one you expected, you iterate on the trigger wording in `AGENTS.md` and rerun. Discoverability is not solved by bloat; it is solved by clarity and feedback.

**The failure mode—under-disclosure.** When lazy loading fails, it is usually because the agent skipped a needed document or routed to the wrong one. This failure mode—under-disclosure—is the central risk. The mitigation is not to "load everything" (that defeats the point) but to invest in better triggers and observability so you *see* when under-disclosure occurs and fix it through iteration.

## Agent Skills: Disclosure One Step Further

The principle of progressive disclosure scales beyond `AGENTS.md` itself. Anthropic's Agent Skills architecture takes the idea one level deeper.

Skills are not named in `AGENTS.md` at all; the agent discovers them by looking in a known folder. Rather than eagerly loading the full skill file, Anthropic's model loads in stages. At startup, only the skill's metadata—its `name` and `description` fields from the YAML frontmatter—is pre-indexed into the system prompt, consuming roughly 100 tokens per skill. That is just enough for the agent to recognize when that skill is relevant. The full skill file (`SKILL.md`) loads only when the agent judges it appropriate. Referenced files load on further demand.

The result: because only metadata is pre-loaded, "the amount of context that can be bundled into a skill is effectively unbounded." Even the *pointers* are disclosed progressively, keeping the always-loaded surface as minimal as possible.

This is progressive disclosure applied to the disclosure mechanism itself—a recursive pattern that scales as agent systems grow more complex.

## Further Reading

The [Learning Hour session plan]({% link index.md %}) walks a team through the mechanism interactively, with a live demo and coding exercise. The [Facilitation Guide]({% link how-to/facilitation-guide.md %}) provides frame-by-frame coaching notes for running the hour.

### References

- [Nielsen Norman Group: Progressive Disclosure](https://www.nngroup.com/articles/progressive-disclosure/) — Jakob Nielsen's canonical definition and history, the primary source for the term and strategy.
- [Anthropic: Effective context engineering for AI agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents) — Primary source for the finite context window, attention budget, context rot, and just-in-time loading.
- [Anthropic: Equipping agents for the real world with Agent Skills](https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills) — Primary source for the Skills progressive-disclosure architecture.
- [Anthropic Claude Platform: Agent Skills overview](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview) — Official product documentation for how Skills load in stages.
- [Augment Code: A good AGENTS.md is a model upgrade…](https://www.augmentcode.com/blog/how-to-write-good-agents-dot-md-files) — Vendor research on optimal AGENTS.md length (100–150 lines) and performance uplift (10–15% improvements across metrics; described as "a quality jump equivalent to upgrading from Haiku to Opus"). Single-source, vendor-internal findings attributed explicitly.
- [InstructionalDesign.org: Minimalism (J. Carroll)](https://www.instructionaldesign.org/theories/minimalism/) — Secondary summary of Carroll's minimalist approach to documentation design and its influence on progressive disclosure.
- [Interaction Design Foundation: Progressive Disclosure](https://www.interaction-design.org/literature/topics/progressive-disclosure) — Corroborating definition and history of the pattern in UX.
- [Diataxis: Explanation](https://diataxis.fr/explanation/) — Authoritative framework for understanding-oriented documentation, which governs the shape of this page.
