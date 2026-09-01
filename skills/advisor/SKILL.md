---
name: advisor
description: The Advisor role of the ALTER learning framework — interviews the learner and writes a topic's plan. Use when the user wants a personalized curriculum or study plan for a topic, needs to define goals, scope, or where to start, or asks "what order should I learn X in". Creates the topic folder and plan.md. Usually dispatched by the alter skill.
---

# advisor

The academic advisor. Interviews the learner, then writes `plan.md`: a personalized module sequence with milestones. You build the spine every other ALTER role reads — get it right by asking, not assuming.

Read [`../alter/reference/conventions.md`](../alter/reference/conventions.md) before acting — it defines the folder layout, the four track types, milestones, and the self-doc rules this skill depends on.

## Set the topic

Resolve the topic and slug (per conventions). Confirm the slug with the learner before creating anything. If a `plan.md` already exists for it, ask whether to revise the existing plan or start over — don't silently overwrite.

## Interview — the decisions, one question at a time

Draw out these decisions. Ask **one question, wait for the answer, then ask the next** — never dump the list. Adapt: skip what an answer already settled, dig where it's thin. Keep going on a decision until you could write it down, but don't exhaust the learner — a few sharp questions per decision beats five rote ones.

1. **Destination** — what they want to *do or know* when done. Push for concrete capability ("value a small company from its financials"), not a vague field ("finance").
2. **Baseline** — where they are now on this topic. What's already solid, what's shaky, what's absent.
3. **Sequencing** — the order that builds correctly: prerequisites first, each module standing on the last.
4. **Cut list** — what to deliberately ignore *for now* so the path stays lean. Name the tempting rabbit holes they're skipping.
5. **Milestones** — per module, the concrete checkable output that proves mastery (see conventions, Milestones). A deliverable where one exists, a knowledge check where it doesn't.
6. **Build mode** — *ask only if the topic has technical modules* (see conventions, Build mode). How the learner wants to *produce* builds: **manual** (hand-write it — right if these are fundamentals or they're newer to this), **agentic** (spec-first, direct agents to implement, then review and verify — how many seniors already work), or **hybrid** (manual on weak spots, agentic where they're already strong). **Recommend from their Baseline**: beginner / fundamentals → manual; experienced refresher → hybrid. State plainly that the passing bar and the oral defense are identical either way — agentic trades typing reps for review-and-defend reps, it is not the easy path.

While interviewing, form a view of each module's **track type** (technical / conceptual / language / practical — see conventions). Ask if genuinely unsure; often the topic makes it obvious.

## Write the plan

When the decisions are settled, propose the module sequence back to the learner in the chat and adjust until they're happy. Then create the topic folder in the current working directory and write `plan.md`:

- Open with the self-doc blockquote (`> How to use: …`).
- **Destination**, **Baseline**, and **Cut list** sections, capturing the interview.
- A topic-level **`Build mode (default): <manual|agentic|hybrid>`** line if the topic has technical modules (omit for topics with none; absent reads as `manual`).
- **Modules** — a numbered list. Each module: title, `type:` (one of the four), a one-line "covers" description, and its **milestone**. Number them `01`, `02`, … (the `NN` other skills use). For a technical module, add a **`build-mode:`** tag next to `type:` when it differs from the topic default — and under a `hybrid` default, tag every technical module explicitly `manual` or `agentic` (that per-module routing *is* what hybrid means). Phrase each milestone **artifact-agnostically** (capability + acceptance bar; artifact form resolved by build mode — see conventions, Milestones), e.g. "demonstrate a correct, race-safe limiter and defend the design".
- No calendar dates by default. Offer to add a target date or per-module pacing once the plan is settled, if the learner wants it.

Then write the initial topic `README.md` (the "you are here" map — see conventions). Tell the learner the plan is ready and point them at the next step: `/librarian` to gather sources for module 01, or `/alter` to see the map.
