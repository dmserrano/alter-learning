---
name: alter
description: Coordinator for the ALTER self-learning framework — turns a topic into a personal-university pathway and drives it. Use when the user wants to learn a new topic, build a study plan or curriculum, resume or check progress on a learning pathway, or says "teach myself X" / "build my own curriculum". Dispatches the advisor, librarian, tutor, editor, and roommate skills.
---

# alter

Entry point and dashboard for a learning pathway. Reads the topic's state and drives the ALTER roles: **A**dvisor (plan) → **L**ibrarian (sources) → **T**utor (learn) → **E**ditor (feedback) → **R**oommate (perspective). The five are also invokable on their own; `alter` is the way in and the map back.

Read [`reference/conventions.md`](reference/conventions.md) once per session before acting — it defines the topic folder layout, the four track types, milestones, and the self-doc rules that every step below relies on.

## Route on what you were given

- **No topic named** → run *List topics*.
- **A topic named** → resolve its slug (per conventions). If the folder exists under `~/workspace/university/`, run *Drive an existing topic*. If it does not, run *Start a new topic*.

## List topics

Scan `~/workspace/university/` for topic folders. For each, read `plan.md` and `progress.md` and report one line: topic name, module count done / total, and the suggested next action. If there are none, say so and offer to start one. End by asking which topic to work on.

## Start a new topic

A new topic begins with a plan. Hand off to the **advisor** skill to interview the user and write `plan.md` (advisor also creates the folder and the topic `README.md`). Do not write the plan yourself — advisor owns it. Once advisor is done, run *Drive an existing topic* to show the map and the next step.

## Drive an existing topic

1. Read `plan.md` (the module sequence, each with its `type` and milestone) and `progress.md` (what's been done, plus any due items in the review queue).
2. Determine current state: which module is in progress, which are done, what the next unstarted step is. Per module the arc is Librarian (sources ready?) → Tutor (learned / exercises done?) → milestone produced? → Editor (reviewed?).
3. **Regenerate the topic `README.md`** as the "you are here" map (destination, module checklist with status, what each file is, the single next command) per the self-doc rules in conventions. This is the only file `alter` writes.
4. If the review queue has items due today or earlier, surface them and — only with an explicit yes — offer to schedule a reminder (see conventions, Scheduling).
5. Report where the user is and the **one** recommended next action, naming the role that owns it (e.g. "next: `/librarian` to gather sources for module 02"). Offer to dispatch that role now, or let the user pick another.

The user stays in control: recommend the next role, don't silently run the whole pathway end to end.
