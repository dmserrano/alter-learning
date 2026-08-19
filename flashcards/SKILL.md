---
name: flashcards
description: Spaced-repetition flashcard drill for an ALTER personal-university topic. Auto-drills the cards that are DUE across all modules (leading with the review queue in progress.md), or drills a specific module on request. Asks one card at a time (dictation-friendly), grades the answer, and reschedules each card by how the learner did. Use when the user says "drill me" / "quiz me" / "flashcards" / "review my due cards", or wants to practice a module's questions. Reads the ## Flashcards blocks in each module's guide.md; usually reached from the tutor or alter skill.
---

# flashcards

A call-response flashcard drill over an ALTER topic. The Tutor writes review sheets (`modules/NN-<name>/guide.md`), each with an optional `## Flashcards` block; this skill is the drill engine that quizzes those cards on a spaced schedule. **Default behavior: drill what's due.** The learner can also scope to one module, or ask for everything.

Read [`../alter/reference/conventions.md`](../alter/reference/conventions.md) before acting — it defines the topic folder layout, the module dirs, and the `progress.md` review queue this skill leans on.

## Resolve topic and gather cards

1. **Find the active topic** (per conventions — named slug, else most-recently-modified topic folder in the working directory; **confirm before drilling**).
2. **Collect cards.** Scan every `modules/NN-<name>/guide.md` for a `## Flashcards` section and read its cards (format below). Note which module each card came from.
3. **Read the schedule.** Each card carries its own due date and Leitner box in a trailing HTML comment; a card with no comment is **new → box 1, due today**. Also read the `progress.md` review queue (`revisit <topic> on/after <date>`) — modules whose topics are due or marked shaky there get their cards prioritized.
4. **If there are no cards anywhere**, say so and point to `/tutor` to write a `guide.md` with a `## Flashcards` block first (offer to draft cards from an existing guide if one exists without them). Don't invent a drill with no source.

## Card format

Cards live in the guide's `## Flashcards` section, one `Q:`/`A:` pair per card. The drill maintains a schedule comment on its own line after each pair — **you write and update this; the learner never has to**:

```
## Flashcards

Q: What does a higher Leitner box mean?
A: The card was answered right repeatedly, so its review interval is longer.
<!-- box 3 · due 2026-09-02 -->

Q: What's the milestone for this module?
A: A working parser that passes the provided test cases.
```

The second card has no comment → treat it as new (box 1, due today). Keep the Q/A prose exactly as the learner/tutor wrote it; only ever add or edit the `<!-- box N · due YYYY-MM-DD -->` line.

## Build the session queue

- **Default (no module named):** all cards whose `due` is **today or earlier**, ordered soonest-due first, with cards from review-queue topics marked shaky in `progress.md` pushed to the front. This is the "drill me on what's due" path.
- **Module named** ("drill me on module 3", "recursion cards"): all cards in that module's guide, due or not.
- **"everything" / "all cards" / "cram":** every card across every module, ignoring due dates.
- If nothing is due and no scope was given, say so, then offer: drill the soonest-upcoming cards anyway, or pick a module. Don't force a session.

Tell the learner the shape of the session in one line before starting ("12 cards due across 3 modules — starting now").

## The drill loop (one card per turn)

Dictation-first, same discipline as the tutor's Socratic teaching — the learner may be speaking and listening via read-aloud:

1. **Ask** the `Q`. Just the question — no preamble, no the answer, no "this one's tricky."
2. **Listen** to their answer.
3. **Grade** silently against the `A`: **got it** / **partial** / **missed**. If they were off, say the correct answer back tersely; if they nailed it, a word of confirmation is enough.
4. **Reschedule** the card in its `guide.md` (see below) — write it immediately, don't batch.
5. Next card, until the queue is empty or they stop.

Keep every turn short. One card, one prompt. No walls of text, no tables mid-drill.

## Rescheduling (Leitner)

Update the card's schedule comment based on the grade, using today's date (see the environment's current date):

- **got it** → box + 1 (cap at 5); set `due` = today + the box interval.
- **partial** → keep the same box; set `due` = today + a short interval (~2 days).
- **missed** → reset to box 1; set `due` = tomorrow.

Box intervals (days): **1→1, 2→3, 3→7, 4→16, 5→35**. So a card answered right out of box 2 becomes box 3, due in 7 days. Write the comment as `<!-- box N · due YYYY-MM-DD -->`, replacing any existing one.

Also reflect the session up in `progress.md`: for a topic the learner keeps missing, add or pull in a `revisit <topic> on/after <date>` review-queue line so the coarse schedule and the tutor stay aware. Don't lose a grade — persist each card's new comment as you go, at minimum on every miss and on stop.

## Spoken commands to honor (match loosely — transcription varies)

- **"hint"** → a short nudge toward the answer, not the answer itself.
- **"skip"** → next card, no grade, no reschedule.
- **"again" / "repeat"** → restate the current question.
- **"show" / "answer"** → reveal the answer, count the card as *missed* for scheduling (they didn't recall it).
- **"score" / "where am I" / "progress"** → done / remaining / how many missed so far.
- **"harder" / "easier"** → bias the remaining queue toward lower / higher boxes.
- **"stop" / "pause" / "that's it"** → finalize all reschedules, give a short wrap-up, end clean.

## Wrap-up (on stop / completion)

Keep it short and speakable:
- Count: drilled / got / missed.
- The 1–2 cards or topics still shaky, and when they next come due.
- If several cards from one module missed, suggest a `/tutor` revisit of that module, not just re-drilling.
- If due cards remain undrilled, say how many and when to come back.

## Scheduling a reminder (opt-in)

If cards are piling up due, you may **offer** to create a real reminder via an available scheduling tool (e.g. the `scheduled-tasks` MCP) — describe exactly what you'd create and get an explicit **yes** first. No scheduling tool available → the due dates stay as the schedule comments and the `progress.md` queue.

## Reusability

Topic-agnostic: point it at any ALTER topic folder and it drills whatever `## Flashcards` blocks that topic's guides contain. All card content and per-card schedule live in the guides — never in this skill.
