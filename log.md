# Log

Append-only. Newest last. Absolute dates.

---

## 2026-09-10 — Wiki created

Set up the LLM wiki following Karpathy's pattern: raw `sources/`, generated
`wiki/`, schema in `CLAUDE.md`.

- Scaffolded the skill tree with 15 skill areas across four bands
  (fundamentals, building, designing, getting hired), all `unseen`.
- Wrote the status ladder: nothing gets promoted without a link to evidence.
- Seeded [[wiki/concepts/two-pointers]] as the worked example of a concept page.

**Open:** [[wiki/meta/profile]] is a stub — target role and timeline unset, so
[[wiki/meta/gaps]] can't be ranked yet and `plan` will be generic until it is.

**Next:** fill in the profile, then `plan`.

## 2026-09-10 — Focus set: DSA / Blind 75, Lesson 1 ready

Narrowed the vault to a single track. Everything outside Band 1 is parked.

- Added the curriculum spine: [[wiki/curriculum/index]] (18 topics, 75 problems;
  topic groups and counts confirmed against the live NeetCode list) and
  [[wiki/curriculum/arrays-hashing]] indexed down to all 8 problems.
- Replaced the `drill` operation with the three-step loop Rob asked for:
  `lesson` → `timed` (30-min box, no hints) → `debrief`. Rules in `CLAUDE.md`.
  Unfinished problems requeue at +2 days rather than being written off.
- Wrote [[wiki/lessons/01-arrays-hashing]]: mental model, hashing mechanics,
  complexity table, the six pattern triggers (A–F), Python traps, and a worked
  example chosen from *outside* the problem set so nothing is spoiled.
- Added [[wiki/meta/resources]] — GfG and LeetCode links, all reachability-checked.
  Dropped one dead GfG URL (`hashing-set-1-introduction`, 404).
- Seeded concepts: [[wiki/concepts/hash-map]], [[wiki/concepts/frequency-map]],
  [[wiki/concepts/complement-lookup]].
- [[wiki/meta/gaps]] re-ranked around DSA; profile stub demoted to Background
  since it no longer blocks.

**Next:** read Lesson 1, then `timed contains-duplicate`.

## 2026-09-13 — Lesson 1 served; first timed box; Contains Duplicate solved cold

The first real evidence in the vault. Everything before this was scaffolding.

- Served [[wiki/lessons/01-arrays-hashing|Lesson 1]] in full: mental models,
  hashing mechanics, the complexity table, patterns A–F, Python traps.
  Rob asked for Pattern F to be expanded — explained the charging argument for
  why the run-start guard keeps it O(n) rather than O(n²), deliberately without
  code, since Pattern F is the tool for problem 8.
- `timed contains-duplicate`, 15:11–15:41 box. Worked in the LeetCode web editor
  by Rob's own choice, to build tolerance for an unfamiliar editor.
- **Solved cold in 7 minutes, unaided, correct first write.** Write-up:
  [[wiki/problems/contains-duplicate]].

**What the debrief found.** The algorithm was never in doubt — he went straight
to hash membership and never considered the nested loop, which is the instinct
Lesson 1 exists to build. Two things below that:

1. Built a `dict` with a dummy value `1` where a `set` belonged. The value is
   never read. Filed the general form of this as the tell to watch for: **a dict
   whose values you never read is a set.**
2. Stated complexity as "time worst = O(n)" — the *average* labelled as the
   worst. True worst is O(n²) when every key collides. New [[wiki/meta/gaps|gap]]
   row; blocks promotion of [[wiki/skills/complexity|Complexity analysis]].

Also: solved in 7 minutes and stopped, leaving 23 minutes of box unused, against
the lesson's own instruction to roll into the next Easy.

- New concept page [[wiki/concepts/seen-set]] — Pattern A had no page while
  B, C and D did, which was an asymmetry worth closing. Promoted to `solid`:
  one clean unaided solve, linked. Not higher — one solve, one shape.
- Cold re-attempt scheduled for **2026-09-16** (+3d). Pass condition written into
  [[wiki/meta/review-queue|the queue]] and it is deliberately stricter than
  working code: `seen = set()`, explicit return on every path, and both
  complexities correct and unprompted.
- [[wiki/meta/gaps]]: "DSA untested" downgraded to 1-of-3 done; "thinking aloud"
  confirmed from real evidence rather than introspection; new complexity gap added.

**Next:** `timed valid-anagram` (topic 1, problem 2), then problem 3, to close
the blocking gap. Re-attempt of Contains Duplicate due 2026-09-16.

## 2026-09-13 (cont.) — Valid Anagram solved cold; the real bottleneck identified

Second box the same afternoon. Both Easies of Topic 1 now solved cold, 7 minutes
each, unaided. Write-up: [[wiki/problems/valid-anagram]].

**The picture this second data point changes.** Two problems, two clean solves,
neither anywhere near the 30-minute box. The algorithms are not the constraint.
What is missing both times is everything *around* the code — the brute force, the
caveats, the reading of the constraints. That reframes the gap ranking: the top
DSA risk is no longer "can he solve it" but "can he show his work while solving
it". [[wiki/meta/gaps]] re-ranked accordingly.

**What the debrief found.**

- Correct first write. Compared the two dicts directly rather than looping over
  one key set — sidesteps the usual bug, worth crediting.
- **Called the space O(n); it is O(1).** The constraints bound the input to
  lowercase English letters, so the maps hold ≤ 26 entries at any input size.
  Generalised into a new concept page,
  [[wiki/concepts/constraint-bounded-complexity]], because it recurs in problems
  4 and 5 and behind the standard "what if it were Unicode?" follow-up.
- Wrote the same counting loop twice instead of extracting it. Did not name
  `Counter`, which §6 of the lesson covers.
- **"On average" missed for the second consecutive debrief.**
- Complexity *was* volunteered unprompted — that was fix #3 from the morning
  debrief, so the coaching landed on that one.
- The narration step was requested in writing before the box and skipped. Asked
  for the brute force at debrief instead; the version given tested the same
  *set* of letters rather than the same *multiset*, missing that each match must
  be consumed. Notable that the O(n²) brute force is harder to get right than
  the O(n) solution — which is itself the argument for narrating up front.

- [[wiki/concepts/frequency-map]] promoted `learning` → `solid` on the solve.
- Cold re-attempts for both Easies due **2026-09-16**.
- Rob chose to stop rather than roll into Two Sum with 23 minutes left —
  deliberately, to take it fresh tomorrow rather than tired.

**Next:** `timed two-sum` on 2026-09-14, which closes the blocking gap. Cold
re-attempts of both Easies due 2026-09-16. Narration is the thing to fix, and
it is now explicitly part of the pass condition, not a suggestion.

---

## 2026-09-14 — All 18 lessons written

The curriculum now has a lesson for every topic, not just topic 1. Written in
parallel, one agent per lesson, against `wiki/lessons/01-arrays-hashing.md` as
the quality bar.

**What landed**

- **18 lesson pages** (`wiki/lessons/02-…` through `18-…`), ~9,200 lines. Each
  follows `templates/lesson.md`: mental model, mechanics, complexity table,
  patterns as *trigger → tool*, traps, a worked example, materials, self-checks.
- **17 new curriculum topic pages.** Every topic is now indexed down to its
  individual problems — the "expand it when the lesson is written" note in
  [[wiki/curriculum/index]] has been discharged. Problem counts sum to 75.
- **56 new concept pages**, taking `wiki/concepts/` from 6 to 62.

**Deliberate constraints**

- **No solutions to anything in the Blind 75.** Every section-8 worked example
  uses a problem outside the set — Squares of a Sorted Array, Minimum Size
  Subarray Sum, Koko Eating Bananas, Baseball Game, Remove Linked List Elements,
  Replace Words. Lessons teach the *trigger*, because recognising the pattern is
  the skill the interview actually tests.
- **Lessons are written to be read in order.** Sliding Window assumes Two
  Pointers; Graphs is built on "a tree is a graph that can't loop back, and the
  one thing graphs add is the visited set"; Greedy is framed as the contrast
  against DP; Backtracking is framed as the un-memoised version of the same
  search. Reading one out of order works but costs more.
- **Every new page is `unseen`.** Nothing was promoted. A written lesson is not
  evidence, and [[wiki/meta/skill-tree]] is untouched as a result — it still
  reflects only the two cold solves from 2026-09-13.

**Bookkeeping**

- [[wiki/lessons/index]], [[wiki/curriculum/index]] and [[wiki/concepts/index]]
  rebuilt; concepts now grouped by the topic that introduces them.
- [[wiki/meta/resources]] points at each lesson's own materials section rather
  than duplicating a flat list.
- Lint: **0 broken wikilinks, 0 orphan pages** across the vault.

**Interruption worth recording.** A usage limit killed 11 of the 17 agents
mid-run. All 18 lesson files had already been written; what was lost was the
tail-end bookkeeping — 2 curriculum pages and 21 concept pages — which four
follow-up agents filled after the reset. Nothing needed rewriting. The
partition that made this cheap was giving each agent sole ownership of its
files and forbidding edits to shared index pages.

**Next:** unchanged by any of this. `timed two-sum`, then the cold re-attempts
of both Easies. See [[wiki/meta/current-plan]].
