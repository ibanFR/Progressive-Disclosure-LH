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

Progressive disclosure is a decades-old design principle with roots in both user interface design and instructional design. Jakob Nielsen, co-founder of the Nielsen Norman Group, frames it as a resolution to a fundamental tension: users want both *power* and *simplicity*. His prescription is elegant: "Initially, show users only a few of the most important options. Offer a larger set of specialized options upon request."

The mechanism is cognitive-load management. By hiding advanced or rarely used features, the design helps novice users avoid mistakes and saves them the mental effort of considering features they don't need. Notably, research shows that people actually understand a system *better* when you help them prioritize — when you help them spend more time on what matters and defer everything else.

A parallel root runs through instructional design. John Carroll's *minimalism*, formalized in the *Minimal Manual* (1987–88), shares the same insight: brevity matters, real tasks matter, exploration matters, and advanced functions should be deferred for beginners. Carroll's "training wheels" interface — temporarily hiding error-prone or specialized functions — reduced learning friction. The concept transfers across 40 years and two domains because the underlying constraint is the same: attention is scarce.

For coding agents, that scarce resource is the context window. `AGENTS.md` *is* documentation-for-an-agent, and the same argument that justified Carroll's minimalism in human manuals justifies progressive disclosure in agent guidance: keep it short, let the reader (or the agent) pull detail on demand.

## Why It Matters for Coding Agents

Three linked mechanical ideas explain why progressive disclosure matters when you orchestrate an LLM-based coding agent.

### The Agent Loop Is Iterative

An agent is an orchestrator that calls the LLM repeatedly, interleaving tool calls (read a file, run a command, propose code) with reasoning. On *every* iteration—not just the first one—the harness re-injects the system prompt, `AGENTS.md`, and the full conversation history into the LLM. Nothing is "paid for once." A line that sits in `AGENTS.md` is re-sent and re-tokenized on pass one, pass two, pass three, and beyond.

### The Context Window Is Finite and Shared

Anthropic states this plainly: context "must be treated as a finite resource with diminishing marginal returns," and "LLMs have an 'attention budget' … Every new token introduced depletes this budget." The window is a shared stack: system prompt, harness instructions, `AGENTS.md`, your task prompts, the agent's working memory, and spare capacity for the actual work. Every line in `AGENTS.md` is a line unavailable for the task itself. Over many iterations, that adds up.

### More Context Comes With a Cost

As tokens accumulate in the context window, the model's ability to accurately *recall* information from that context decreases. Anthropic calls this "context rot." So a bloated `AGENTS.md` is doubly costly: it consumes the finite budget *and* can actually degrade the model's attention on what matters most.

Put together, these three ideas point to a hard trade-off: include everything people *want* in `AGENTS.md`, and the agent pays a compounding performance cost on every iteration. Defer the detail to on-demand loading, and the agent stays sharp—but only if it can reliably *find* what it needs when it needs it.

## The Hub-and-Spoke Model

Progressive disclosure resolves this trade-off through a structural pattern. `AGENTS.md` and the harness sit at the hub. Knowledge documents—Test Design Advice, Class Design Advice, Commit Message Guidelines, and the like—radiate as spokes. The model enforces two rules:

1. **Tell the agent *when* to read each document.** The trigger lives in `AGENTS.md`; the content lives elsewhere. A one-sentence pointer in the hub tells the agent *why* and *when* to load the whole document from a spoke.
2. **Whole documents load only when needed.** Nothing enters the context window until the moment it is actually relevant.

This dissolves the tension visible in the Learning Hour: the list of things a team *wants* in `AGENTS.md` is enormous—coding standards, commit conventions, architectural constraints, refactoring patterns, project context, repo layout, build instructions. Progressive disclosure doesn't ask teams to cut content; it asks them to *defer* it. Keep the hub short by holding pointers, not detail. The spokes hold the full story.

The payoff: `AGENTS.md` stays lean, the agent has spare context capacity on every loop, and the model's attention budget goes where it matters.

## Context Engineering: The Larger Frame

Progressive disclosure is one tactic within a larger discipline that Anthropic calls **context engineering**—the set of strategies for curating and maintaining the optimal set of tokens during LLM inference. It succeeds prompt engineering as agents run long, multi-turn tasks. The exact prescription Anthropic endorses is called "just-in-time" loading: agents "maintain lightweight identifiers (file paths, stored queries, web links, etc.) and use these references to dynamically load data into context at runtime using tools."

That is exactly the hub-and-spoke pattern: pointers at the hub, content on demand. Progressive disclosure, in Anthropic's framing, is context engineering made concrete.

## Trade-Offs and Tensions

Like any design principle, progressive disclosure involves trade-offs. Understanding them helps explain when the pattern works well and where it strains.

**Eager loading vs. lazy loading.** Progressive disclosure bets on lazy loading: load only when triggered. The alternative is eager loading—put everything important into `AGENTS.md` and pay the cost on every iteration. Eager loading guarantees availability but invites context rot. Lazy loading keeps the window lean but shifts the burden: the agent must *know* when to load a document.

**Explicit triggers vs. retrieval.** Progressive disclosure relies on human-written triggers in `AGENTS.md` pointing to documents. An alternative is embedding-based retrieval (RAG), where the system searches a knowledge base at inference time. Triggers add authoring burden but are explicit and deterministic. Retrieval is automatic but introduces infrastructure, relevance risk, and adds latency. Anthropic distinguishes the two: "just-in-time" loading (triggers) vs. "embedding-based pre-inference retrieval." Both can work; they trade off simplicity against automation.

**Discoverability vs. minimalism.** A lean hub is cleaner and cheaper. But a team that knows `AGENTS.md` is short may not *look* for a trigger to something they need. By contrast, a rich hub makes everything visible—but at the cost of context bloat and the very problem we are trying to solve. The practical answer is better triggers and observability. The emoji verification trick addresses this: each knowledge document emits an emoji when read, so a facilitator can *see* in the output which documents were actually loaded. If the agent skipped one you expected, you iterate on the trigger wording in `AGENTS.md` and rerun. Discoverability is not solved by bloat; it is solved by clarity and feedback.

**The failure mode—under-disclosure.** When lazy loading fails, it is usually because the agent skipped a needed document or routed to the wrong one. This failure mode—under-disclosure—is the central risk. The mitigation is not to "load everything" (that defeats the point) but to invest in better triggers and observability so you *see* when under-disclosure occurs and fix it through iteration.

## Agent Skills: Disclosure One Step Further

The principle of progressive disclosure scales beyond `AGENTS.md` itself. Anthropic's Agent Skills architecture takes the idea one level deeper.

Skills are not named in `AGENTS.md` at all; the agent discovers them by looking in a known folder. Rather than eagerly loading the full skill file, Anthropic's model loads in stages: at startup, only the skill's metadata (name and description) is pre-indexed into the system prompt—just enough for the agent to recognize when that skill is relevant. The full skill file (`SKILL.md`) loads only when the agent judges it appropriate. Referenced files load on further demand.

The result: because only metadata is pre-loaded, "the amount of context that can be bundled into a skill is effectively unbounded." Even the *pointers* are disclosed progressively, keeping the always-loaded surface as minimal as possible.

This is progressive disclosure applied to the disclosure mechanism itself—a recursive pattern that scales as agent systems grow more complex.

## Further Reading

The [Learning Hour session plan]({% link index.md %}) walks a team through the mechanism interactively, with a live demo and coding exercise. The [Facilitation Guide]({% link how-to/facilitation-guide.md %}) provides frame-by-frame coaching notes for running the hour.

### References

- [Nielsen Norman Group: Progressive Disclosure](https://www.nngroup.com/articles/progressive-disclosure/) — Jakob Nielsen's canonical definition and history, the primary source for the term and strategy.
- [Anthropic: Effective context engineering for AI agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents) — Primary source for the finite context window, attention budget, context rot, and just-in-time loading.
- [Anthropic: Equipping agents for the real world with Agent Skills](https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills) — Primary source for the Skills progressive-disclosure architecture.
- [Anthropic Claude Platform: Agent Skills overview](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview) — Official product documentation for how Skills load in stages.
- [Augment Code: A good AGENTS.md is a model upgrade…](https://www.augmentcode.com/blog/how-to-write-good-agents-dot-md-files) — Vendor research on optimal AGENTS.md length (100–150 lines) and performance uplift (10–15%). Single-source findings; attributed as a vendor blog.
- [InstructionalDesign.org: Minimalism (J. Carroll)](https://www.instructionaldesign.org/theories/minimalism/) — Secondary summary of Carroll's minimalist approach to documentation design.
- [Interaction Design Foundation: Progressive Disclosure](https://www.interaction-design.org/literature/topics/progressive-disclosure) — Corroborating definition and history of the pattern in UX.
