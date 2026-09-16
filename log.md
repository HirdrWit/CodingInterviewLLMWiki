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

## 2026-09-14 — Two Sum solved cold; blocking gap closed; process landed

Write-up: [[wiki/problems/two-sum]]. 11 minutes of a 30 minute box, unaided,
correct first write.

**The box where the process happened.** Brute force typed as a comment before
the solution, complexity volunteered with the average-case caveat, space stated
without being asked — all three fixes from the 2026-09-13 debriefs, landing
together on the first attempt after being asked three times.

**Blocking gap closed.** All three Easies of Topic 1 attempted, debriefed and
solved cold: 7 min, 7 min, 11 min. That answers the question the vault was built
to answer — the ceiling on Easies is not the binding constraint. Moved to the
closed-gaps table in [[wiki/meta/gaps]] with the evidence, rather than deleted.

**What the debrief found.**

- Checked before inserting, so self-pairing is *structurally impossible* rather
  than guarded against afterwards. That is the bug in this problem and it wasn't
  written. Computed the complement on its own line, which shows the actual
  insight — search for the partner, not the pair.
- Explicit `return []` on the unreachable path. Correct instinct, worth defending
  out loud rather than looking accidental.
- **Naming was the only real weakness.** `val` for the complement; `store` for
  `seen`; `.get()` inside a guarded branch. The name *is* the insight here —
  `complement = target - num` explains itself, `val = ...` doesn't.
- Answered the O(min(n, k)) question correctly when asked: Valid Anagram's k=26
  binds → O(1), Two Sum's k≈2·10⁹ doesn't → O(n). Yesterday's concept holding up
  under a reversal. Counted as prompted, not volunteered.
- **One process step still missing in all three boxes: naming the rejected
  alternative.** Here it's a good one — sort + two pointers is O(1) space but the
  problem returns *indices*, and sorting destroys them. New gap row.

- [[wiki/concepts/complement-lookup]] promoted `learning` → `solid`.
- Gap counters now tracked as fractions (narration 1 of 3, caveat 1 of 3,
  constraints 1 of 2) rather than pass/fail, so partial progress is visible.
- [[wiki/skills/complexity|Complexity analysis]] still held at `learning`: the
  caveat appeared but the worst case is unspoken and the constraint reasoning
  was prompted.

**This week's plan is complete** — [[wiki/meta/current-plan]] targeted problems
1–3 attempted and debriefed; all three are solved cold instead. Plan is now
stale and should be replaced by a `plan` run before the Mediums start.

**Next:** cold re-attempts due 2026-09-16 (Contains Duplicate, Valid Anagram)
and 2026-09-17 (Two Sum). Then Topic 1 problems 4–8, all Mediums. Expect
unfinished boxes — that is what the Mediums are for.

## 2026-09-14 (cont.) — `plan` run; profile read for the first time

Ran `plan` after Two Sum. Replaced [[wiki/meta/current-plan]] wholesale.

**The significant finding is not in the DSA work.** [[wiki/meta/profile]] had
been filled in at some point before this session and was read for the first time
tonight — it was modified in the working tree at session start and got swept into
the day's commit unread. What it says contradicts the premise the vault was
built on:

Target role **Software Engineering Manager**, level **Senior Engineer**, target
company **Vivint Smart Home** (Boston hybrid), **7 years** experience, CS degree,
strongest language **Golang**.

[[CLAUDE.md]] frames the goal as "get Rob a software engineering job" with bands
2–4 parked until topics 1–3 are cold-solvable. That is correct strategy for
someone with no track record. For a 7-year engineer targeting senior or EM it is
probably wrong in emphasis: system design and behavioral carry the most weight in
both loops and are both `unseen`, while Blind 75 completion is a gate rather than
the finish line. Flagged in the plan; the parking of bands 2–4 should expire
earlier than currently written. **Not acted on unilaterally — this is Rob's call
and CLAUDE.md is his file.**

Also raised, not decided:
- **Role and level on file describe two different loops.** SEM adds people/org
  rounds and usually weights coding lighter. Doesn't change this week's work, so
  not blocking, but it governs everything after.
- **Solving in Python while Golang is 7 years deep.** A real decision, currently
  being made by default.
- Hours per week, target date, and definition of done are still blank, so every
  volume number in the plan is a guess and is labelled as one.

**Plan written:** Topic 1 finished over 2026-09-14 → 09-20. Re-attempts first in
any session (09-16 ×2, 09-17 ×1), then Mediums 4–8 in order. Realistic target is
problems 4–6 plus all three re-attempts passing; 7–8 stretch. Volume deliberately
not raised — three Easies at 7–11 minutes says the Easies were never the
constraint. The four process gaps are the actual target and are trained inside
the existing boxes at no extra time cost.

Proposed but not done: stub a Vivint company page. A named target exists and
`wiki/companies/` is empty.

**Next:** `timed group-anagrams` (#4, first Medium).

## 2026-09-14 (cont.) — direction corrected by Rob; DSA stays primary

Rob pushed back on the rebalance suggested an hour earlier, and the pushback is
right. The inference was "7 years + EM title → the gap is design and behavioral",
which is the standard senior-hire read. He has information the profile doesn't
carry: the behavioral and design reps already exist from doing the job. **DSA
stays primary.** [[wiki/meta/current-plan]] rewritten to record the corrected
direction rather than the inferred one.

**Three things came out of the exchange that are worth more than the correction.**

**1. The IC/manager fork is deliberately unresolved.** Rob is undecided. The prep
overlaps almost entirely until applications start, so nothing is blocked. Noted
an asymmetry: EM credibility doesn't decay while doing DSA, but IC credibility
decays while not coding. The management experience is banked; the hands-on claim
is the perishable one. The DSA work is therefore what keeps both doors open,
independent of which gets used.

**2. New hypothesis about what "weak at DSA" means here.** Three cold solves,
7/11 minutes, **zero algorithmic errors and zero wrong turns** across three
boxes — while every single miss was in the surrounding work (brute force,
caveats, constraints, naming). That reads as fluent in the ideas and rusty at the
performance, not weak pattern recognition. Consistent with four years of
management plus AI doing the generative work. Recorded in the plan explicitly as
a **hypothesis, not a finding** — three Easies from one pattern family is thin,
and problems 4–8 this week confirm or kill it. If it holds, the implication is
fewer problems done out loud, not more reps.

New [[wiki/meta/gaps|gap]] row, ranked Serious: coding fluency from an empty file,
unaided and observed — distinct from algorithm knowledge.

**3. Standing rule added: no assistance inside a box.** No Copilot, no Claude, no
autocomplete. Rob raised AI erosion himself and it is the sharper of the two
decays he named — recent, and invisible. Already satisfied by working in the
LeetCode web editor; now written down rather than incidental.

Behavioral and design left `unseen` on the [[wiki/meta/skill-tree|skill tree]]
with "claimed solid" noted inline — the evidence rule, not a judgment. Offered a
cheap conversion: one post-mortem page on a real design led at Vivint. Not taken
up; not pushed.

**Next:** unchanged — `timed group-anagrams` (#4, first Medium).

---

## 2026-09-16 — first failed re-attempt, and it is the useful kind

`timed group-anagrams` did not happen on Mon 09-15; the plan's Monday slot went
unused and problem 4 is still `unseen`. Not backfilled — it just moves.

**Cold re-attempt: [[wiki/problems/contains-duplicate|Contains Duplicate]], 3 min
32 s. Scored `fail`.** Requeued for 2026-09-19.

The code was perfect and arrived from memory in half the original time. Every
named fix from 2026-09-13 survived three days unprompted: `seen = set()` instead
of a dict with an unread placeholder, the name `seen` instead of `result`, and
membership checked before insert. **That half of the problem is retired** — the
next re-attempt will not re-test it.

The fail is one line. Complexity was volunteered without being asked (the
narration habit fired, 2 of 3 now), but the sentence was *"Time: O(n) worst"* —
the average case wearing the worst case's label. The true worst is O(n²) under
colliding keys. Identical to the 2026-09-13 miss, and the pass condition written
on that date named this exact string as a fail, in advance. Honouring it.

Space was **right**, and unprompted, and right for the right reason: `k ≈ 2·10⁹`
does not bind, so O(min(n,k)) resolves to O(n). That gap moves to 2 of 2.

**The finding worth keeping.** Across four boxes now:

> Zero algorithmic errors. Four debriefs of analysis-and-narration misses.

Every error this vault has recorded is in the talking, not the coding. That is
the [[wiki/meta/current-plan|plan]]'s hypothesis — fluent in the ideas, rusty at
the performance — picking up its first *re-attempt* evidence rather than more
first-attempt evidence. It is still four Easies from one pattern family, so the
Mediums remain what confirms or kills it.

Practical consequence recorded in [[wiki/curriculum/arrays-hashing|Topic 1]]: for
the rest of this topic, **the complexity sentence is the deliverable, not the
code.**

[[wiki/meta/gaps|Gaps]] updated: the hash average-case caveat resets to 0 of 3
and is promoted to the top of Serious — highest-frequency miss on record.
"Names the approach rejected" is still 0, now across four boxes, and remains the
cheapest gap on the list to close.

[[wiki/concepts/seen-set|Seen-Set]] **kept at `solid`** rather than demoted. The
queue's default rule demotes on a failed re-attempt, but the concept itself was
demonstrated cleanly, twice; what failed was complexity analysis, which is
tracked as its own gap. Demoting the data-structure concept would have put a
false statement on the scoreboard. Flagged for Rob rather than done silently.

**Next:** [[wiki/problems/valid-anagram|Valid Anagram]] re-attempt is due today
and still open. Then `timed group-anagrams` (#4, first Medium).

## 2026-09-16 (cont.) — Valid Anagram re-attempt: second fail, same shape

**Cold re-attempt: [[wiki/problems/valid-anagram|Valid Anagram]], 5 min 53 s
(was 7 min). Scored `fail`.** Requeued to 2026-09-19, alongside Contains
Duplicate.

I called it a pass in the debrief and then corrected myself against the written
pass condition: *"deduplicated, O(1) space stated correctly and unprompted, and
`Counter` named."* Two of three. `Counter` was named as a required fix on
2026-09-13 and still was not said. Having failed Contains Duplicate on one
pre-stated line an hour earlier, a softer standard here would have made the
scoreboard worthless.

**Real progress, recorded as such.** The complexity was **decomposed** for the
first time — per-element cost separated from the loop, average/worst attached to
the hash operation rather than to the whole algorithm — and O(1) space was
volunteered with the bounded-alphabet justification. That was the headline miss
on 2026-09-13. It is fixed. Best analysis on record.

**The teaching moment of the day**, now written into
[[wiki/curriculum/arrays-hashing|Topic 1]] as a table: the two Easies
re-attempted back to back make a point neither makes alone. `k ≈ 2·10⁹` in
Contains Duplicate does not bind → space O(n), time worst **O(n²)**. `k = 26` in
Valid Anagram does bind → space O(1), and therefore a collision scan is over ≤26
entries, so time is **O(n) worst case included**. Same formula, opposite answers.
Rob had both halves of that argument in hand today and used it only on space.

**Where the day leaves things.** Two re-attempts, two fails, **zero code
errors** — one solution byte-identical to the target, the other correct and
deduplicated. Across six boxes now the vault has recorded no algorithmic mistake
and nothing but analysis-and-presentation misses.

The queue has stopped testing whether Rob can solve these and started testing
whether he can **present** them. Noted in [[wiki/meta/review-queue|the queue]] as
a standing note. Every Serious row in [[wiki/meta/gaps|gaps]] is now a process
row.

"Names the approach rejected" is **0 across 5 boxes** — named in the
Valid Anagram write-up itself on 2026-09-13 as "cheap and reads as senior", and
still never once attempted. It is the cheapest item on the board.

**Next:** `timed group-anagrams` (#4) — the first Medium, still unattempted after
Monday's slot went unused. It is the box that tests whether the
"fluent in ideas, rusty at performance" hypothesis survives contact with a
harder problem, since all six data points so far are Easies.
