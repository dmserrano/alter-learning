# ALTER conventions

The shared contract for the ALTER learning skills (`alter`, `advisor`, `librarian`, `tutor`, `editor`, `roommate`) and the `flashcards` drill helper. Each skill points here instead of restating these rules, so a change to the layout or the type system is a one-place edit. Read the section you need; you rarely need all of it.

ALTER builds a **personal university**: a self-directed learning pathway for one topic, captured as Markdown in a topic folder. The five roles are Advisor (plan), Librarian (sources), Tutor (teach + practice), Editor (feedback), Roommate (cross-domain perspective). `alter` is the coordinator over them.

## Where topics live

Every topic is a folder in the **current working directory** — the directory the skill was invoked from. One topic per folder; a topic folder is any subdirectory containing a `plan.md`. (Run the ALTER skills from wherever you want your learning to live — e.g. a dedicated `university/` directory — and topics accumulate there.)

```
./                       # the directory you ran the skill from
  <topic-slug>/
    README.md            # "you are here" map — alter regenerates this every run
    plan.md              # advisor: destination, baseline, module sequence, cut-list
    reading-list.md      # librarian: ranked vetted sources, tied to modules
    prompts.md           # librarian, on demand: pluggable prompts for other tools
    progress.md          # tutor: dated session log + review queue
    modules/
      NN-<name>/         # everything for one module lives here
        README.md        # what this module holds (self-doc dir)
        tutorial.md      # tutor, technical: step-by-step build
        guide.md         # tutor, on request or at milestone: review sheet (optional ## Flashcards for drills)
        exercises/
          <name>/        # tutor, technical: scaffolded exercise you build in
        milestone/       # YOUR deliverable for this module + a README with its acceptance check
        review.md        # editor: critique of the milestone (+ optional diff)
    capstone-<name>/     # tutor, technical: one exercise applying many modules (topic-level, spans modules)
    roommate.md          # roommate, on demand: cross-domain provocations log
```

`NN` is the two-digit module number from `plan.md` (`01`, `02`, …); `<name>` is a short slug of the module title, so `modules/03-recursion/`. A file or dir only exists once the owning skill has run for that module — an early-stage topic may hold just `README.md` and `plan.md`, and `modules/` appears once the Tutor starts the first module. Because the module number is in the dir name, the files inside drop it (`tutorial.md`, not `tutorial-03.md`).

**Slug**: derive `<topic-slug>` from the topic name in lower-kebab-case (`"Claude dev skills"` → `claude-dev-skills`). Offer it; let the user override.

## Migrating an existing topic

When this layout changes, an older topic folder is brought up to date by **reconciling it against the current layout above** — no separate migration tool needed. Any ALTER skill (usually `alter`, since it already reads the whole topic) can do it on request. The procedure:

1. **Read this file first** so you're moving toward the *current* layout, not a remembered one.
2. **Detect the old shape.** Compare what's on disk to the layout above and list every file/dir that has moved. The most recent move consolidated per-module files under `modules/NN-<name>/`: `tutorial-NN.md` → `modules/NN-<name>/tutorial.md`, `guide-NN.md` → `.../guide.md`, `exercises/NN-<name>/` → `.../exercises/`, `milestones/NN-<name>/` → `.../milestone/`, `feedback/NN-<name>-review.md` → `.../review.md`. Topic-level files (`plan.md`, `reading-list.md`, `prompts.md`, `progress.md`, `roommate.md`, `capstone-<name>/`) stay put.
3. **Show the move plan and confirm before touching anything** — a rename table, oldest-to-newest. Moving learner work is not something to do silently.
4. **Move with `git mv`** where the topic is a git repo (preserves history); plain move otherwise. Preserve file **content** exactly — only the path changes. Drop the now-redundant module number from filenames inside the numbered dir (`tutorial.md`, not `tutorial-03.md`).
5. **Fix internal references.** Update relative links between the moved files (e.g. a `README.md` pointing at `../milestones/03-x/`) and add any newly-required `README.md` for a moved directory that lacks one.
6. **Regenerate the topic `README.md`** last (that's `alter`'s job) so the "you are here" map reflects the new paths and the Due-for-review line.

Migrations are expected to be **rare and mechanical**; keep this a documented reconciliation, not a bespoke script. If layout churn ever becomes frequent, that's the signal to extract a dedicated migrate skill — not before.

## Track types

The Advisor tags every module in `plan.md` with one `type`. The type is the switch that decides how the Librarian, Tutor, and milestone behave for that module. Four types:

- **technical** — building something (code, tools, configs). Librarian favors official docs, APIs, repos, first-party courses. Tutor writes a `modules/NN-<name>/tutorial.md` plus scaffolds `modules/NN-<name>/exercises/<name>/` the learner builds in, and may set one topic-level `capstone-` exercise spanning several modules. Milestone defaults to a **working deliverable**.
- **conceptual** — theory / knowledge (design theory, history, business finance). Librarian favors books, essays, papers, lectures. Tutor is interactive Socratic teach/test; no scaffold. Optional `modules/NN-<name>/guide.md` on request. Milestone defaults to a **written artifact or a knowledge check**.
- **language** — a natural language. Librarian favors grammar references, graded input, native media. Tutor runs vocab/grammar drills and conversation practice with live correction, and leans on the review queue for spaced repetition. Milestone defaults to a **conversation or translation checkpoint**.
- **practical** — a real-world doing skill (cooking, chess, public speaking, drawing). Librarian favors technique guides, exemplars, demos. Tutor runs deliberate-practice drills: learner does it, describes or records it, gets feedback. Milestone defaults to a **performance artifact** the Editor reviews.

## Milestones

A milestone is a **concrete, checkable output that proves the learner can do the thing** — "write a recursive parser that passes these cases", not "understand recursion". Every module ends in exactly one milestone. When a module has no natural deliverable, the milestone is a **knowledge check**: a Tutor quiz the learner must pass. The learner's milestone work lives in `modules/NN-<name>/milestone/` and is what the Editor reviews.

## Self-documenting output

Every file ALTER generates orients the reader:

- **Every generated Markdown file** opens with a one-line blockquote: `> How to use: <what this is> · owned by /<skill> · next: <what to do>`.
- **Every generated directory** gets a `README.md` saying what it holds and which skill owns it.
- **Each topic's `README.md`** is the "you are here" map and the topic-level dashboard: the destination, a checklist of modules with status (done / in progress / not started) from `plan.md` + `progress.md`, a **Due for review** line (which modules have flashcards or review-queue items due now — the one place to see what `/flashcards` would drill), what each file is, and the single suggested next command. The **advisor writes the first version** when it creates the topic; **`alter` regenerates it in full on every run**. In addition, **any skill that changes a module's status (Tutor finishing teaching, Editor passing a milestone) refreshes that module's checklist line and the Due-for-review line in place** — a cheap, targeted edit so the dashboard stays current between `alter` runs. Keep that refresh light: update the status marks and a due count from files already in hand; don't do an expensive re-scan. Only `alter` rewrites the whole file.

## Finding the active topic

A sub-skill invoked directly (not through `alter`) resolves its topic this way: use the slug if the user named one; otherwise pick the most-recently-modified topic folder in the current working directory and **confirm it with the user before acting**. Never guess silently.

## Scheduling (opt-in)

The Tutor maintains a **review queue** in `progress.md` (items marked `revisit <topic> on/after <date>`) at topic granularity. Finer per-card scheduling lives in the `## Flashcards` blocks of each module's `guide.md`: the **`flashcards`** skill stamps every card it drills with a `<!-- box N · due YYYY-MM-DD -->` comment (Leitner spacing) and leads each session with the cards now due. When reviews or cards are due, the Tutor, `flashcards`, or `alter` may **offer** to create a real reminder via an available scheduling tool (e.g. the `scheduled-tasks` MCP). Creating a scheduled task is side-effectful: always describe what will be created and get an explicit yes first. If no scheduling tool is available, the schedule stays as the due dates in the guides and the notes in `progress.md`.
