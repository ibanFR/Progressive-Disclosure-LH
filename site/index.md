---
title: Home
layout: home
nav_order: 1
---

# Progressive Disclosure
{: .no_toc }

A [Learning Hour](https://sammancoaching.org/reference/learning_hour_definition.html) on keeping `AGENTS.md` short and letting coding agents pull in knowledge documents **on demand** — so the context window stays free for real work.
{: .fs-6 .fw-300 }

This Learning Hour (#GLH053, by Emily Bache, she/her) walks a team through *why* a bloated `AGENTS.md` hurts, and *how* progressive disclosure fixes it. A frame-by-frame [Facilitation Guide]({% link how-to/facilitation-guide.md %}) accompanies the [Miro board](https://miro.com/app/board/uXjVHPh12To=/); this page is the session plan. Materials are shared under a Creative Commons Share-alike license.

## Table of Contents
{: .no_toc .text-delta }

1. TOC
{:toc}

## Learning Goals 🎯

By the end of the hour, participants can:

1. **Use progressive disclosure** in `AGENTS.md` — hold triggers and pointers, not full detail.
2. **Describe *why*** we keep `AGENTS.md` short — every loop iteration re-sends it into a finite context window.
3. **Use emojis** to track which knowledge documents the agent actually read.

## Connect — What goes in `AGENTS.md`? (⏱️ 5 min)

Surface what the team already knows before explaining anything, using the [Three Facts](https://sammancoaching.org/activities/connect/three_facts.html) Samman Connect activity.

**Prompt:** *"Write three kinds of information you put — or would put — in `AGENTS.md`."*

- Everyone adds sticky notes (individually or in pairs) with three concrete examples each — ~2 min to write, then cluster and read aloud.
- Don't evaluate answers yet; the goal is to activate prior knowledge and expose the range.
- Expect: coding standards, commit conventions, architectural constraints, refactoring patterns, pointers to more specific docs, project context and goals, repo layout, build/TDD instructions.

**Bridge:** Notice how *long* this list is getting — hold that thought.

See the full list of [Connect Activities](https://sammancoaching.org/activities/connect.html) for alternatives.

## Concept — Why keep `AGENTS.md` short? (⏱️ 12 min)

Three linked ideas build the motivation for progressive disclosure.

**1. Design advice.** **100–150 lines is optimal** for `AGENTS.md`. A well-written one can deliver value comparable to a model upgrade — the best files improve agent performance by roughly **10–15%** over teams with no such documentation (source: the [Augment Code blog](https://www.augmentcode.com/)).

**2. The Agent Loop.** Name the three components — the **Agent** (orchestrator), the **LLM** (reasoning), and **Tool Calls** (actions). The loop is *iterative* (the agent may call tools many times before returning). Crucially, on **every** iteration the harness injects `AGENTS.md` + the system prompt + the full conversation history into the LLM. Nothing is free — a bloated `AGENTS.md` is paid for on every pass, not once.

**3. The Context Window.** It is a *finite* stack everyone shares: system prompt · `AGENTS.md` & harness · your prompts · agent working memory · spare capacity. Every line spent on a permanent layer is a line unavailable for real work. This is *why* 100–150 lines matters.

**Set up the reveal:** *"What if a document only entered the context window at the moment it was actually needed?"*

See [Concept Activities](https://sammancoaching.org/activities/concept.html) for more ideas.

## Concept — Live demo (⏱️ 5 min)

Model the exact workflow participants will practise.

**Demo prompt:** *"Improve the design of this code."*

- Open the sample repo, give the agent the short prompt, referencing one source file.
- Narrate which knowledge documents the agent chooses to read **on demand**.
- Show the emoji trick: knowledge docs emit an emoji so you can *see* in the output which ones were loaded. Ask the group to watch for the emojis — it primes them for the exercise.

Keep it tight and observable; the point is the *mechanism*, not a polished result.

## Concept — Progressive Disclosure, the core idea (⏱️ 3 min)

**"Progressive Disclosure: Minimize Context."** A hub-and-spoke model: `AGENTS.md` & the harness sit at the centre; knowledge documents (Test Design Advice, Class Design Advice, Commit Message Advice) are read *on demand*.

Two rules:

- **Tell the agent *when* to read each document** — the trigger lives in `AGENTS.md`, the content lives elsewhere.
- **Whole documents are only loaded when needed** — nothing enters the context window until its moment.

This resolves the Connect tension: `AGENTS.md` stays short by holding *pointers and triggers*, not full detail → more spare capacity → better, cheaper agent performance.

## Concrete Practice — Coding exercise (⏱️ 20 min)

**Repository:** [Progressive-Disclosure-LH](https://github.com/ibanFR/Progressive-Disclosure-LH) (based on the [Encode-TestDesign-Kata](https://github.com/emilybache/Encode-TestDesign-Kata) by Emily Bache).

**Core prompt:** *"Improve the design of this code."*

1. Use a simple prompt with **one** source code file.
2. Inspect the agent's output.
3. Review *which* knowledge documents were used (watch the emojis).
4. Evaluate the results and iterate on the documents if needed.
5. Once the code is ready, create a new knowledge document for commit messages.

**Facilitator notes:** Circulate — the interesting learning is in steps 3–4, where participants see whether the agent read the documents they expected. If it ignored one or read the wrong one, adjust the trigger wording in `AGENTS.md` and rerun. Suggest everyone start from the *same* file so results are easy to compare in the debrief. Step 5 gives fast learners a natural extension.

See [Concrete Practice Activities](https://sammancoaching.org/activities/concrete.html) for more ideas.

## Concept — Skills: disclosure, one step further (⏱️ 3 min)

An optional deepening for advanced learners.

- Skills are **not mentioned in `AGENTS.md`** at all.
- The agent **looks in a known folder** to discover available skills.
- Only the **first 3 lines** of each skill file are exposed for indexing/preview — the full skill loads only when selected.

Even the *pointers* can be disclosed progressively, keeping `AGENTS.md` shorter still. Mention it, but don't let it derail teams still digesting the core idea.

## Conclusions — Discussion and reflection (⏱️ 5 min)

Draw the learning out of the participants rather than restating it.

**Prompt:** *"What is progressive disclosure and why is it useful for coding agents?"*

- Collect answers on sticky notes, then cluster the themes aloud.
- Aim to have the group articulate the through-line: short `AGENTS.md` → smaller context footprint → more spare capacity → better, cheaper agent performance.
- Themes to listen for: preventing context bloat, improving performance / reducing cost, lazy/conditional loading, managing the agent's attention, structuring knowledge documents, avoiding irrelevant information.

Close by linking back to the three opening objectives — did we meet them? — and reinforce the emoji verification trick as a practical takeaway.

See [Conclusions Activities](https://sammancoaching.org/activities/conclusions.html) for more ideas.

## References

- [Facilitation Guide]({% link how-to/facilitation-guide.md %}) — frame-by-frame walkthrough
- [Miro board](https://miro.com/app/board/uXjVHPh12To=/)
- [Progressive-Disclosure-LH exercise repository](https://github.com/ibanFR/Progressive-Disclosure-LH)
- [Encode-TestDesign-Kata](https://github.com/emilybache/Encode-TestDesign-Kata) by Emily Bache
- [Harness Engineering](https://www.youtube.com/watch?v=JaiJ5wxdmCA) — Emily Bache (video) on guides, sensors, and the improvement flywheel for agentic AI
- [Learning Hour](https://sammancoaching.org/reference/learning_hour_definition.html)
- [Connect](https://sammancoaching.org/activities/connect.html) · [Concept](https://sammancoaching.org/activities/concept.html) · [Concrete Practice](https://sammancoaching.org/activities/concrete.html) · [Conclusions](https://sammancoaching.org/activities/conclusions.html) Activities
- [Samman Coaching](https://sammancoaching.org/)
