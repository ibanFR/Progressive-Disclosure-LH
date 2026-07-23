---
title: Facilitation Guide
layout: default
parent: How-to Guides
nav_order: 1
---

# Facilitation Guide
{: .no_toc }

A frame-by-frame guide for facilitating the **Progressive Disclosure** Learning Hour
(#GLH053, by Emily Bache). 
{: .fs-6 .fw-300 }

Each section below corresponds to one frame on the
[Miro board](https://miro.com/app/board/uXjVHPh12To=/), in left-to-right presentation order. The session follows
the [Learning Hour](https://sammancoaching.org/reference/learning_hour_definition.html)
4C structure: **Connect → Concept → Concrete → Conclusions**.

## Table of Contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Progressive Disclosure — Title Slide

**Role:** Title / welcome slide · **Phase:** Intro · **⏱️ 2 min**

Open the session here. Introduce yourself and the topic, then set expectations against
the three learning objectives shown on the slide.

**Learning objectives to read out:**

- Use progressive disclosure in `AGENTS.md`.
- Understand *why* we keep `AGENTS.md` short.
- Use emojis to track which knowledge documents the agent actually used.

**Facilitator notes:**

- Keep this brief — it is a framing slide, not a discussion.
- Mention that materials are shared under a Creative Commons Share-alike license
  and credit the original author (Emily Bache, she/her).
- Signal the shape of the hour: a Connect discussion, a Concept explanation, a hands-on
  coding exercise, and a wrap-up reflection.

---

## Connect - AGENTS.md

**Phase:** Connect · **⏱️ 5 min**

The Connect activity surfaces what participants already know, so everyone starts thinking
about the topic before you explain anything.

**Prompt to pose:** *"What kinds of information do you put in `AGENTS.md`? Give three examples."*

**How to run it:**

- Ask everyone to add sticky notes (individually or in pairs) with concrete examples.
- Give ~2 minutes for writing, then cluster and read the notes aloud.
- Don't evaluate answers yet — the goal is to activate prior knowledge and expose the range.

**Answers you can expect** (already captured on the frame): team coding standards, commit
message conventions, architectural constraints, refactoring patterns, rules, pointers to
more specific docs, high-level architecture notes, coding preferences, TDD usage and build
instructions, general project context and goals, purpose, rough repository layout.

**Bridge to the next phase:** Notice how *long* this list is getting. Hold that thought —
we'll return to it when we talk about keeping `AGENTS.md` short.

---

## Design advice - AGENTS.md

**Phase:** Concept · **⏱️ 3 min**

First concept slide. Give the participants a concrete, evidence-backed guideline for
`AGENTS.md`.

**Key point:** **100–150 lines is optimal** for an `AGENTS.md` file.

**Supporting evidence to share:** A well-written `AGENTS.md` can deliver value comparable
to a model upgrade — the best files improve agent performance by roughly **10–15%** over
teams with no such documentation (source: the
[Augment Code blog](https://www.augmentcode.com/)).

**Facilitator notes:**

- Tie this back to the Connect activity: the group just generated *far more* than 150 lines
  worth of ideas. That tension — "everything is useful" vs. "keep it short" — is exactly what
  progressive disclosure resolves.
- Pose the question the rest of the session answers: *if we can't put everything in `AGENTS.md`,
  where does the rest go, and when does the agent read it?*

---

## The Agent Loop (1 of 2)

**Phase:** Concept · **⏱️ 4 min**

The first of two "Agent Loop" frames. Use this diagram to build shared vocabulary for how a
coding agent actually works before introducing the context window.

**Walk through the six steps** (numbered on the frame):

1. Prompt and reference files
2. Instructions to the LLM
3. Tool instructions
4. Tool response
5. LLM response
6. LLM response (formatted)

**Facilitator notes:**

- Name the three components clearly: the **Agent** (orchestrator), the **LLM** (reasoning),
  and **Tool Calls** (actions against the codebase).
- Emphasise the loop is *iterative* — the "n-times" notation means the agent may call tools
  many times before returning a final result.
- This frame is the plain version of the loop; the next frame layers in where `AGENTS.md`
  enters, so keep it simple here.

---

## The Agent Loop (2 of 2)

**Phase:** Concept · **⏱️ 4 min**

The second "Agent Loop" frame extends the first by showing *what the harness injects* into
the loop. This is the key "aha" that motivates progressive disclosure.

**What's new on this frame:** Step 2 ("Instructions to LLM") is highlighted, with a callout:
**"Adds `AGENTS.md`, System Prompt & existing conversation."**

**Facilitator notes:**

- Make the point explicit: every time the loop runs, `AGENTS.md` + the system prompt + the
  full conversation history are sent to the LLM *again*. Nothing is free.
- This is the reason length matters — a bloated `AGENTS.md` is paid for on *every* iteration,
  not once.
- Hand off to the Context Window frame with a question: *"So what is all this text competing
  for space in?"*

---

## Context Window

**Phase:** Concept · **⏱️ 4 min**

The payoff concept. This diagram shows the context window as a fixed stack that everything
must share.

**Walk through the stack (top to bottom):**

- **System prompt** — foundational instructions (can be large).
- **`AGENTS.md` & harness** — project framework and operational structure.
- **Your prompts** — user input.
- **Agent working** — tool calls, opened files, changes made, reasoning.
- **Spare capacity** — what's left for actual work.

**Facilitator notes:**

- The core message: the window is *finite*. Every line spent on a permanent system/`AGENTS.md`
  layer is a line not available for the agent's real working memory.
- Connect the dots: this is *why* 100–150 lines matters, and why we can't just dump every
  knowledge document into `AGENTS.md`.
- Set up the reveal: *"What if a document only entered the context window at the moment it was
  actually needed?"* — that is progressive disclosure.

---

## Demo - Progressive Disclosure

**Phase:** Concept (live demo) · **⏱️ 5 min**

A short live coding demo before participants try it themselves. It models the exact workflow
they'll practice.

**The demo prompt:** *"Improve the design of this code."*

**How to run the demo:**

- Open the sample repository and give the agent the short prompt above, referencing one source
  file.
- Narrate as you go: point out *which* knowledge documents the agent chooses to read on demand.
- Show the emoji trick in action — have your knowledge docs emit an emoji so you can *see* in
  the output which ones were loaded.

**Facilitator notes:**

- Keep the demo tight and observable; the point is the *mechanism* (on-demand loading), not a
  polished result.
- Explicitly ask the group to watch for the emojis — this primes them for the exercise.

---

## Progressive Disclosure — Core Concept

**Phase:** Concept (the core idea, visualised) · **⏱️ 3 min**

This frame is the conceptual centrepiece: **"Progressive Disclosure: Minimize Context."**
It shows how `AGENTS.md` & the harness stay small — holding only triggers and pointers — while the
knowledge documents are read *on demand*.

**Documents loaded only when needed:** Test Design Advice, Class Design Advice,
Commit Message Advice, and `AGENTS.md` itself.

**Two rules to emphasise (from the sticky notes):**

- **Tell the agent *when* to read each document** — the trigger lives in `AGENTS.md`, the
  content lives elsewhere.
- **Whole documents are only loaded when needed** — nothing enters the context window until
  its moment.

**Facilitator notes:**

- This resolves the tension from the Connect activity: `AGENTS.md` stays short by holding
  *pointers and triggers*, not the full detail.
- Reinforce: short `AGENTS.md` + on-demand documents = more spare capacity in the context window.

---

## Exercise - Progressive Disclosure

**Phase:** Concrete Practice · **⏱️ 20 min**

The hands-on coding exercise. Participants practise using different knowledge documents with a
short prompt.

**Repository:** [Progressive-Disclosure-LH](https://github.com/ibanFR/Progressive-Disclosure-LH)
(based on the [Encode-TestDesign-Kata](https://github.com/emilybache/Encode-TestDesign-Kata) by Emily Bache).

**Core prompt:** *"Improve the design of this code."*

**Five-step instruction sequence:**

1. Use a simple prompt with one source code file.
2. Inspect the agent's output.
3. Review *which* knowledge documents were used (watch the emojis).
4. Evaluate the results and iterate on the documents if needed.
5. Once the code is ready, create a new knowledge document for commit messages.

**Facilitator notes:**

- Circulate while participants work; the interesting learning is in step 3–4, where they see
  whether the agent read the documents they expected.
- Encourage iteration: if the agent ignored a document or read the wrong one, adjust the trigger
  wording in `AGENTS.md` and rerun.
- The commit-message document in step 5 gives fast learners a natural extension.
- Consider suggesting everyone start from the *same* file so results are easy to compare in the
  debrief (a piece of feedback from a previous run of this session).

---

## Skills

**Phase:** Concept (extension / advanced) · **⏱️ 3 min**

An optional deepening: how *Skills* implement progressive disclosure a step further than plain
document pointers.

**Key ideas from the frame:**

- Skills are **not mentioned in `AGENTS.md`** at all.
- Instead, the agent **looks in a known folder** to discover available skills.
- Only the **first 3 lines of each skill file** are exposed for indexing/preview — the full
  skill is loaded only when selected.

**Facilitator notes:**

- Frame this as the next level of the same principle: even the *pointers* can be disclosed
  progressively, keeping `AGENTS.md` shorter still.
- This is a good "advanced learner" tangent — mention it, but don't let it derail teams still
  digesting the core idea.

---

## Conclusions

**Phase:** Conclusions · **⏱️ 5 min**

Wrap-up discussion. Draw the learning out of the participants rather than restating it yourself.

**Prompt to pose:** *"What is progressive disclosure and why is it useful for coding agents?"*

**How to run it:**

- Collect answers on sticky notes, then cluster the themes aloud.
- Aim to have the group articulate the through-line themselves: short `AGENTS.md` → smaller
  context footprint → more spare capacity → better, cheaper agent performance.

**Themes to listen for** (already captured on the frame): preventing context bloat / managing
window size, improving agent performance and reducing cost, lazy/conditional loading of
instructions, managing the agent's attention, structuring knowledge documents, organising files
across environments, and avoiding irrelevant information.

**Facilitator notes:**

- Close by linking back to the three opening objectives — did we meet them?
- Reinforce the emoji verification trick as a practical takeaway they can use tomorrow.

---

## Feedback

**Phase:** Retrospective (facilitator reference) · **Not shown to participants live**

This frame captures feedback from a previous run of the session. Use it to prepare and to avoid
known pitfalls — it is a facilitator resource, not a slide to present.

**What worked well:**

- The Connect activity helped surface the audience's starting experience.
- The kata stayed focused on the learning objectives at the right level.
- The Agent Loop visualisation and the context-window image explained the mechanics clearly.
- A small, clean sample repository (no bloat) kept attention on the concept.
- Emojis made the content engaging *and* served as a verification mechanism for context usage.
- Beginner-friendly, while the meta-level debugging trick still rewarded advanced learners.

**Watch-outs / adjustments:**

- **Terminology:** be deliberate about "harness + LLM" vs. "harness + agent" — the distinction
  confused some participants. Note that the inference server is what runs the model.
- **Accessibility:** the checkmark emoji renders light grey and is hard to see in dark mode —
  choose a high-contrast emoji.
- **Comparability:** consider having everyone examine the *same* file so outputs are easier to
  compare in the debrief.
- Consider adding links to `CLAUDE.md` / tool-specific documentation for teams using those tools.
