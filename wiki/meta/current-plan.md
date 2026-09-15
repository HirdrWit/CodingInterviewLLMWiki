---
type: meta
updated: 2026-09-14
sources: []
tags: [spine]
---

# Current Plan

Replaced wholesale each time `plan` runs. History lives in [[../../log|log.md]].

---

## Direction, confirmed 2026-09-14

[[profile]] was filled in and read for the first time on 2026-09-14. Initial
read of it suggested rebalancing away from DSA toward system design and
behavioral — the standard senior-hire advice. **Rob corrected that, and the
correction stands:** the behavioral and design reps are already there from doing
the EM job. DSA is the self-assessed weakest area and stays primary.

| | On file / stated |
|---|---|
| Target role | **Undecided: IC or manager.** Not forced — see below |
| Level | Senior Engineer / SEM |
| Target company | Vivint Smart Home (S&P tech), Boston hybrid |
| Experience | 7 years, CS degree |
| Strongest language | Golang (currently solving in Python) |
| Self-assessed | DSA weakest; behavioral and design solid; **rusty on hands-on coding since moving to management and since AI tooling** |

### The IC / manager fork is deliberately not being resolved

The prep overlaps almost entirely until late — coding screen and system design
appear in both loops, and the EM-specific rounds are the part already covered by
doing the job. The fork only starts mattering at the point of choosing which
reqs to apply to.

There is an asymmetry worth keeping in view: **EM credibility does not decay
while doing DSA; IC credibility decays while not coding.** The management
experience is banked. The hands-on claim is the perishable one. So the DSA work
is what keeps both doors open, independent of which one gets walked through.

### The working hypothesis about what "weak at DSA" actually means

Evidence so far: 3 problems, 3 cold solves, 7 / 7 / 11 minutes, correct on first
write, **zero algorithmic errors and zero wrong turns.** Right structure reached
for every time. What was missing in all three was everything *around* the code —
brute force, caveats, constraints, naming.

That is not the signature of weak pattern recognition. It reads as **fluent in
the ideas, rusty at the performance** — which is what four years of management
plus AI-assisted coding would predict. The muscle that atrophies is generating
correct, well-named code from an empty file, at speed, while observed.

**This is a hypothesis, not a finding** — three Easies from one pattern family.
Problems 4–8 this week confirm or kill it. If boxes start getting blown on the
Mediums, it is wrong and the plan gets revised.

If it holds, the implication is: **fewer problems, done out loud, code written to
be read.** Not more pattern-recognition reps.

### Standing rule: no assistance inside a box

**No Copilot, no Claude, no autocomplete, ever, inside a timed box.** A problem
touched with assistance is worth ~zero as evidence and poisons the re-attempt.
Working in the LeetCode web editor already satisfies this — now it's a rule, not
a habit.

Optional, worth considering: one small thing a week written by hand with no
assistant. Not DSA — just the generative muscle.

### Parked, with a cheap action available

Behavioral and design remain `unseen` on the [[skill-tree|skill tree]]. That is
the evidence rule, not a judgment — nothing in the vault links to a design doc, a
solve, or an interview.

Worth separating **doing the job well** from **performing it in a 45-minute loop
against a stranger's rubric.** The second is a distinct skill, and strong
practitioners lose those rounds by telling the story of a system rather than the
story of their decisions in it.

Not this month. But one page — a post-mortem of one real design led at Vivint,
with the constraints, the options, the choice and its cost — converts banked
experience into interview-ready material at very low cost. Available on request.

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

1. **Hours per week**, and **target date for an offer.** Both blank in
   [[profile]]. Without them every volume number here is a guess and is labelled
   as one.
2. **Golang or Python for interviews?** Currently being decided by default.
   Python is the lower-friction practice language and the lesson recommends it —
   but you interview in the language you think in, and Go is 7 years deep.
   Decide deliberately before the first real screen, not at it.
3. **Definition of done** in [[profile]] is unwritten. Worth five minutes — it is
   what stops this becoming indefinite study.
4. ~~SEM or Senior Engineer?~~ **Deliberately deferred.** Not blocking; the prep
   overlaps until applications start.
