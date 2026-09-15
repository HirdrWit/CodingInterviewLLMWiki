---
type: meta
updated: 2026-09-14
sources: []
tags: [spine]
---

# Current Plan

Replaced wholesale each time `plan` runs. History lives in [[../../log|log.md]].

---

## First: the profile changed the picture

[[profile]] is no longer a stub, and what's in it does not match the assumption
the vault was built on. Recorded here because it should drive everything
downstream:

| | On file |
|---|---|
| Target role | **Software Engineering Manager** |
| Level | **Senior Engineer** |
| Target company | **Vivint Smart Home** (S&P tech), Boston hybrid |
| Experience | **7 years**, CS degree |
| Strongest language | **Golang** (solving in Python) |

Three consequences, in order of how much they change the work.

**1. This is not a junior job search, and [[../../CLAUDE|CLAUDE.md]] is written
as though it were.** "Get Rob a software engineering job" with bands 2–4 parked
is the right strategy for someone with no track record. For a 7-year engineer
with a CS degree targeting senior or EM, the binding constraint is much less
likely to be Blind 75 completion and much more likely to be **system design and
behavioral** — those carry the most weight in both loops and are both `unseen`
on the [[skill-tree|skill tree]]. DSA still has to be cleared; it is a gate, not
the finish line. The parking of bands 2–4 should expire sooner than "topics 1–3
cold-solvable".

**2. Role and level on file describe two different interview loops.** SEM and
Senior Engineer overlap on coding and system design, but the EM loop adds people
management, org, and hiring scenarios, and usually weights the coding screen
lighter. Planning past this week needs to know which one. **Top open question —
see the bottom of this page.** This week's work is identical under both readings,
so it isn't blocking yet.

**3. Solving in Python while Golang is 7 years deep is a live decision, not a
detail.** Python is the lower-friction DSA language and the lesson recommends it.
But interview in the language you think in, unless the company says otherwise —
and 7 years of Go beats 2 weeks of Python muscle memory under pressure. Worth
deciding deliberately rather than by default. Not urgent; decide before the
first real screen.

## Focus this week: finish Topic 1, and train the process

The DSA track continues — momentum is real, and both loops contain a coding
screen. But the volume target is deliberately *not* raised. Three Easies solved
cold in 7–11 minutes says the Easies were never the constraint, so grinding more
of them proves nothing.

## Week of 2026-09-14 → 2026-09-20

**Re-attempts come first in any session. Always.** The queue emptying is the real
signal of progress; the problem count is not.

| Date | Work |
|---|---|
| Mon 09-15 | `timed group-anagrams` (#4, Medium, Pattern D) + `debrief` |
| Tue 09-16 | **Re-attempt: Contains Duplicate, Valid Anagram** (cold, due). Then `timed top-k-frequent` (#5) if there's appetite |
| Wed 09-17 | **Re-attempt: Two Sum** (cold, due). Then `timed encode-decode-strings` (#6) |
| Thu 09-18 | `timed product-of-array-except-self` (#7, Pattern E) |
| Fri 09-19 | `timed longest-consecutive-sequence` (#8, Pattern F) |
| Sat/Sun | Buffer. Anything that requeued at +2d, and `lint` |

**Realistic target: problems 4–6 attempted and debriefed, all three re-attempts
passed.** Problems 7–8 are stretch. If a Medium goes unfinished it requeues at
+2 days and comes back — that is the system working, not a slipped schedule.

This assumes roughly one 45–60 minute session per day. **Hours per week is still
blank in [[profile]]**, so that number is a guess; correct it and the table gets
rebuilt honestly.

## What the Mediums are actually for

Expect the first unfinished box here. Nothing about three fast Easies predicts
problems 4–8 — they're a different tier, and locating the real ceiling is their
job. An unfinished Medium is the most useful data the vault has collected so far.

## The process work — this is the real target

All three remaining [[gaps|gaps]] are process, not algorithms. They are trained
inside the boxes above, at no extra time cost:

- **Narration — 1 of 3.** Brute force + complexity in the editor before the real
  solution. It held once, on an Easy. The test is whether it holds on a Medium
  where the pressure is real.
- **The full complexity sentence — 1 of 3.** Average *and* worst, both spoken.
- **Constraints read into the space bound — 1 of 2.** Volunteered, not prompted.
- **Name the approach rejected, and why — 0 of 2.** Never done in three boxes.
  Easiest of the four to fix and the most senior-sounding in a real loop.

Reference card: [[../../notes/Lesson 1 - Arrays-Hashing|notes/Lesson 1]].

## Also this week, if there's time

**Stub [[../companies/index|wiki/companies/vivint]].** There is a named target
company on file and the companies folder is empty. A page with the stack, the
public JDs for senior/EM, and what their loop looks like would start making the
DSA work concrete instead of generic. Say `ingest <JD url>` and it writes itself.

## Open questions, ranked

1. **SEM or Senior Engineer?** Different loops, different preparation past this
   week. The single highest-value thing to resolve.
2. **Hours per week**, and **target date for an offer.** Both blank. Without them
   every volume number here is a guess.
3. **Golang or Python for interviews?** Decide before the first real screen.
4. **Definition of done** in [[profile]] is unwritten. Worth five minutes —
   it's what stops this becoming indefinite study.
