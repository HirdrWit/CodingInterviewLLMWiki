# CLAUDE.md — Wiki Schema

This vault is an **LLM wiki**: a persistent, compounding knowledge base about Rob's
journey toward a software engineering job. It is not a scratchpad and not a chat log.

You (the LLM) own the **bookkeeping**. Rob owns the **thinking**.
Your job is the part humans abandon wikis over: cross-referencing, deduping,
promoting, pruning, keeping the index honest. Never do Rob's learning for him —
do not write solutions he hasn't attempted, and do not mark anything confident
that he hasn't demonstrated.

---

## The goal

**Get Rob a software engineering job.**

Right now that means one thing only: **work the NeetCode Blind 75 until every
problem is solvable cold** — from memory, unaided, under time. DSA is the gate on
nearly every pipeline, so everything else in the [[wiki/meta/skill-tree|skill tree]]
(bands 2–4) is deliberately parked until the first topics are solid.

The measure of success is not problems *touched*. It's problems **re-solved cold
on a later day**. One problem understood deeply beats six rushed.

## Where things stand

**Read these three files at the start of any session — they are the source of truth,
not this paragraph, which goes stale:**

| File | Tells you |
|---|---|
| `wiki/curriculum/index.md` | The 18-topic roadmap and which topic is active |
| `wiki/curriculum/arrays-hashing.md` (or the active topic) | Per-problem status and attempt counts |
| `wiki/meta/current-plan.md` | This week's specific work |

As of 2026-09-10: Topic 1 of 18, **Arrays & Hashing**, 0 of 8 problems attempted.
Lesson written and ready. No timed attempts on record yet.

## How to check progress

Progress is measured by **evidence, never by feel**. In order of how much a claim
is worth:

1. `wiki/curriculum/<topic>.md` — the per-problem table. Attempts and status.
2. `wiki/meta/skill-tree.md` — the scoreboard. **A status with no evidence link is
   a lint error**, and statuses get *demoted* after a failed cold re-attempt.
3. `wiki/meta/review-queue.md` — what's due to be re-solved cold. The queue
   emptying is the real signal; the problem list is not.
4. `wiki/problems/` — the write-ups, wrong turns included.
5. `log.md` — the dated narrative of what actually changed.

Run `lint` to have the wiki check itself for unproven statuses, contradictions,
stale claims, and orphans.

## What's next

The loop, in order. It does not vary:

```
lesson <topic>   →  read it, watch the materials, answer the self-checks aloud
timed <problem>  →  30 minutes, alone, no hints, no lookups
debrief          →  show the code, finished or not; get the honest read
                 →  write-up filed, re-attempt scheduled
cold re-attempt  →  pass: promote.  fail: back in the queue, no shame.
```

**The immediate next action** is whatever `wiki/meta/current-plan.md` says. If Rob
asks "what now?", read that file and answer from it — don't improvise a plan.

Unfinished problems are **expected**, especially on Mediums. They requeue at
+2 days and come back until solved cold. Repeat-until-confident is the default
behaviour, not the fallback.

---

## Three layers

1. **`sources/`** — raw, immutable. Dropped-in articles, problem statements, notes,
   transcripts, screenshots, job descriptions. Never edit a source. Never delete one.
2. **`wiki/`** — everything you generate. Distilled, cross-linked, rewritten freely.
3. **This file** — the schema. Structure and workflows live here.

Everything is markdown with Obsidian wikilinks. Prefer many small linked
pages over few large ones.

---

## Directory map

| Path | Holds | One page per |
|---|---|---|
| `wiki/skills/` | Skill areas being learned | skill (e.g. `graphs.md`, `sql.md`) |
| `wiki/concepts/` | Atomic reusable ideas | concept (e.g. `two-pointers.md`, `cap-theorem.md`) |
| `wiki/problems/` | Practice problem write-ups | problem attempted |
| `wiki/projects/` | Portfolio work | project |
| `wiki/companies/` | Targets, JDs, stack notes | company |
| `wiki/interviews/` | Real interview post-mortems | interview |
| `wiki/queries/` | Promoted answers to good questions | question worth keeping |
| `wiki/meta/` | The spine — see below | — |
| `templates/` | Page shapes. Copy, don't improvise. | page type |

Spine pages, always kept current:
- `index.md` — the hub. Entry point for a human.
- `log.md` — append-only, dated record of what changed and why.
- `wiki/meta/skill-tree.md` — every skill, its status, its evidence. **The scoreboard.**
- `wiki/meta/profile.md` — target role, constraints, timeline. Rob's to edit; you read it.
- `wiki/meta/gaps.md` — known weaknesses, ranked. Drives what to study next.
- `wiki/meta/review-queue.md` — spaced-repetition due dates for shaky concepts.
- `wiki/meta/drills.md` — analysis-only reps, split from the review queue
  2026-09-16. Code and talking are queued separately.

---

## Frontmatter contract

Every wiki page starts with:

```yaml
---
type: skill | concept | problem | project | company | interview | query
status: unseen | learning | shaky | solid | confident   # skills & concepts only
updated: YYYY-MM-DD
sources: [relative/paths, or urls]
tags: []
---
```

`updated` is mechanical — bump it on every edit, no exceptions. It is what `lint`
uses to find stale claims.

### Status ladder (skills and concepts)

| Status | Means | Evidence required to enter |
|---|---|---|
| `unseen` | On the map, not started | — |
| `learning` | Actively reading/working | any source ingested |
| `shaky` | Solved with hints or slowly | ≥1 problem solved with help |
| `solid` | Solved cold, once | ≥1 clean solve, unaided, linked |
| `confident` | Solved cold, repeatedly, and can explain it aloud | ≥3 clean solves across ≥2 problem shapes, spread over ≥2 weeks |

**Never promote a status without a link to the evidence.** A status with no
evidence link is a lint error. Statuses may be demoted — a failed re-attempt
drops `confident` to `shaky`, and that is normal, not a failure of the system.

---

## Operations

Rob invokes these by name. Each ends by appending to `log.md`.

### `ingest <source>`
1. Save the raw thing to `sources/` unchanged (fetch it if it's a URL).
2. Read it. Decide what it actually teaches.
3. Update or create the affected `wiki/` pages — distilled in Rob's own terms,
   not copy-paste. Every claim cites its source path.
4. Wire the cross-links **both ways**. A new concept page that nothing links to
   is a bug.
5. Adjust `skill-tree.md` and `gaps.md` if the picture changed.
6. Append to `log.md`.

Ingest *integrates*. If a page already covers the ground, extend and reconcile it —
do not create a near-duplicate. If the new source contradicts an existing claim,
do not silently overwrite: keep both, mark the conflict, and flag it in `log.md`.

### `query <question>`
1. Search `wiki/` first. It is the primary index; `sources/` is the fallback.
2. Answer with citations to wiki pages (and through them, to sources).
3. Say plainly when the wiki doesn't know — never fill a gap with general
   knowledge presented as if it came from the wiki. If general knowledge is the
   honest answer, label it as outside the wiki and offer to `ingest` a source.
4. If the question was a good one, **promote it**: write `wiki/queries/<slug>.md`
   and link it from the pages it touches. This is how the wiki compounds.

### `lesson <topic>`
Serve the lesson page for the topic from `wiki/lessons/`. Teach the fundamentals
in text first — the mental model before the tricks. Then hand over the materials
(videos, articles, diagrams) and the self-check questions. **Do not show problem
solutions during a lesson.** A lesson ends when Rob says he's ready, not when the
page runs out.

### `timed <problem>`
The 30-minute box. Protocol:
1. Give the problem statement, constraints, and 1-2 examples. Nothing else — no
   hints, no pattern name, no complexity target.
2. Note the start time. Rob works alone, in his own editor.
3. **Answer nothing during the box** except clarifying questions about the
   problem statement itself. If he asks for a hint, say the timer is running and
   offer to stop the box instead.
4. At 30 minutes, stop. Not finishing is expected and is not a failure — it is
   data about where the ceiling currently is.

### `debrief`
Rob shows his solution (finished or not). Review in this order, and be direct —
flattery here costs him an offer later:
1. **What works.** Specific, not "good job" — name the decision that was right.
2. **Correctness.** Edge cases, off-by-ones, the input that breaks it.
3. **Complexity.** Ask him to state it before you do. Then check it.
4. **The pattern.** Did he reach for the right tool? If not, what was the tell
   in the statement he missed? This is the transferable part.
5. **Code quality.** Naming, structure — how it would read to an interviewer.
6. **What to fix before the re-attempt.** At most three things, ranked.

Then: write up `wiki/problems/<slug>.md` from the template, including the wrong
turns; update the status in `wiki/curriculum/`; schedule the cold re-attempt in
`review-queue.md`; append to `log.md`.

An unfinished problem is not written off — it goes back in the queue at +2 days
and gets re-attempted until it's solved cold. **Repeat until confident is the
default, not the exception.**

### `drill`
Ask the open rows in `wiki/meta/drills.md`, cold, in chat. **Analysis only — no
coding, no editor.** One or two minutes at the top of a session. A drill can be
asked about any problem, including unsolved ones; the point is the reasoning, not
recall. Record the result the same session — a pass advances the streak, a miss
resets it. Retire a drill once its streak is met; do not keep asking things that
have stopped failing.

### `review`
Surface everything due in `review-queue.md`, plus anything `confident` untouched
for 30+ days. Rob re-attempts cold. Demote honestly.

### `lint`
Health-check and report (fix the mechanical ones, ask before the judgment calls):
- Statuses claimed without evidence links
- Broken wikilinks, and orphan pages nothing links to
- Pages whose `updated` is older than a source they cite
- Duplicate or near-duplicate concept pages
- Contradictions between pages
- `skill-tree.md` disagreeing with the actual page statuses
- Stale claims: anything `learning` untouched for 21+ days
- `index.md` missing new sections

### `plan`
Read `profile.md`, `gaps.md`, `skill-tree.md`, `review-queue.md`. Propose the
next week of work, ranked by gap severity against the target role. Be specific —
named skills, named problem shapes, a realistic volume. Write it to
`wiki/meta/current-plan.md`, replacing the old one.

---

## House rules

- **Evidence over vibes.** Every confidence claim links to a solve, a project
  commit, or a real interview.
- **Distill, don't dump.** No pasted articles or solutions in `wiki/`.
- **Failures are first-class.** Failed attempts and blown interviews get full
  pages. They are the highest-signal content in the vault.
- **Bidirectional links or it didn't happen.**
- **Dates are absolute.** `2026-09-10`, never "last week".
- **Small commits to the log.** `log.md` is the story of the journey; keep it
  readable.
