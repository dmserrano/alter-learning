# ALTER conventions

The shared contract for the ALTER learning skills (`alter`, `advisor`, `librarian`, `tutor`, `editor`, `roommate`). Each skill points here instead of restating these rules, so a change to the layout or the type system is a one-place edit. Read the section you need; you rarely need all of it.

ALTER builds a **personal university**: a self-directed learning pathway for one topic, captured as Markdown in a topic folder. The five roles are Advisor (plan), Librarian (sources), Tutor (teach + practice), Editor (feedback), Roommate (cross-domain perspective). `alter` is the coordinator over them.

## Where topics live

Every topic is a folder in the **current working directory** — the directory the skill was invoked from. One topic per folder; a topic folder is any subdirectory containing a `plan.md`. (Run the ALTER skills from wherever you want your learning to live — e.g. a dedicated `university/` directory — and topics accumulate there.)

```
./                     # the directory you ran the skill from
  <topic-slug>/
    README.md          # "you are here" map — alter regenerates this every run
    plan.md            # advisor: destination, baseline, module sequence, cut-list
    reading-list.md    # librarian: ranked vetted sources, tied to modules
    prompts.md         # librarian, on demand: pluggable prompts for other tools
    progress.md        # tutor: dated session log + review queue
    tutorial-NN.md     # tutor, technical modules: step-by-step build
    guide-NN.md        # tutor, non-technical, on demand: study guide
    exercises/
      NN-<name>/       # tutor, technical: scaffolded exercise you build in
      capstone-<name>/ # tutor, technical: one exercise applying many modules
    milestones/
      NN-<name>/       # YOUR deliverables — the work you produce
    feedback/
      NN-<name>-review.md  # editor: critique of a milestone (+ optional diff)
    roommate.md        # roommate, on demand: cross-domain provocations log
```

`NN` is the two-digit module number from `plan.md` (`01`, `02`, …). A file or dir only exists once the owning skill has run for that module — an early-stage topic may hold just `README.md` and `plan.md`.

**Slug**: derive `<topic-slug>` from the topic name in lower-kebab-case (`"Claude dev skills"` → `claude-dev-skills`). Offer it; let the user override.

## Track types

The Advisor tags every module in `plan.md` with one `type`. The type is the switch that decides how the Librarian, Tutor, and milestone behave for that module. Four types:

- **technical** — building something (code, tools, configs). Librarian favors official docs, APIs, repos, first-party courses. Tutor writes a `tutorial-NN.md` plus scaffolds `exercises/NN-<name>/` the learner builds in, and may set one `capstone-` exercise spanning several modules. Milestone defaults to a **working deliverable**.
- **conceptual** — theory / knowledge (design theory, history, business finance). Librarian favors books, essays, papers, lectures. Tutor is interactive Socratic teach/test; no scaffold. Optional `guide-NN.md` on request. Milestone defaults to a **written artifact or a knowledge check**.
- **language** — a natural language. Librarian favors grammar references, graded input, native media. Tutor runs vocab/grammar drills and conversation practice with live correction, and leans on the review queue for spaced repetition. Milestone defaults to a **conversation or translation checkpoint**.
- **practical** — a real-world doing skill (cooking, chess, public speaking, drawing). Librarian favors technique guides, exemplars, demos. Tutor runs deliberate-practice drills: learner does it, describes or records it, gets feedback. Milestone defaults to a **performance artifact** the Editor reviews.

## Milestones

A milestone is a **concrete, checkable output that proves the learner can do the thing** — "write a recursive parser that passes these cases", not "understand recursion". Every module ends in exactly one milestone. When a module has no natural deliverable, the milestone is a **knowledge check**: a Tutor quiz the learner must pass. The learner's milestone work lives in `milestones/NN-<name>/` and is what the Editor reviews.

## Self-documenting output

Every file ALTER generates orients the reader:

- **Every generated Markdown file** opens with a one-line blockquote: `> How to use: <what this is> · owned by /<skill> · next: <what to do>`.
- **Every generated directory** gets a `README.md` saying what it holds and which skill owns it.
- **Each topic's `README.md`** is the "you are here" map: the destination, a checklist of modules with status (done / in progress / not started) from `plan.md` + `progress.md`, what each file is, and the single suggested next command. The **advisor writes the first version** when it creates the topic; **`alter` regenerates it on every run** so it always reflects current progress; no other skill edits it.

## Finding the active topic

A sub-skill invoked directly (not through `alter`) resolves its topic this way: use the slug if the user named one; otherwise pick the most-recently-modified topic folder in the current working directory and **confirm it with the user before acting**. Never guess silently.

## Scheduling (opt-in)

The Tutor maintains a **review queue** in `progress.md` (items marked `revisit <topic> on/after <date>`). When reviews are due, the Tutor or `alter` may **offer** to create a real reminder via an available scheduling tool (e.g. the `scheduled-tasks` MCP). Creating a scheduled task is side-effectful: always describe what will be created and get an explicit yes first. If no scheduling tool is available, the queue stays as plain notes in `progress.md`.
