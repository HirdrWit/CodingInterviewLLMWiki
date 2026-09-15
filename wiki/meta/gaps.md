---
type: meta
updated: 2026-09-14
sources: []
tags: [spine]
---

# Gaps

Ranked weaknesses. Drives what `plan` and the next `timed` problem pick.

Ranking is **severity against the target role in [[profile]]**, not difficulty.
Each gap: what's missing, how it was discovered, what would close it.

---

## Blocking
*Would sink an interview this week.*

| Gap | Discovered via | Closes when |
|---|---|---|
| — | — | — |

*Nothing blocking. The DSA-untested gap closed 2026-09-14 — see Closed gaps.*

## Serious
*Will show up soon.*

| Gap | Discovered via | Closes when |
|---|---|---|
| **Coding fluency from an empty file, unaided and observed.** Rusty since moving to management and since AI tooling took over the generative work. Distinct from algorithm knowledge — 3 boxes produced zero algorithmic errors but recurring naming and structure weakness | self-reported 2026-09-14, consistent with all 3 debriefs to date | 2 consecutive Mediums finished inside the box with clean naming, no assistance |
| Complexity analysis unproven — stating time *and* space unprompted is a scored behaviour, not a bonus | [[../lessons/01-arrays-hashing\|Lesson 1]], 2026-09-10 | 3 consecutive debriefs where complexity was stated correctly without being asked |
| Narration under a timer — **1 of 3 consecutive boxes done.** Still top DSA risk until it holds under a Medium, where the pressure is real | [[../problems/contains-duplicate\|Contains Duplicate]] 2026-09-13 (silent); [[../problems/valid-anagram\|Valid Anagram]] 2026-09-13 (skipped when asked); **done** [[../problems/two-sum\|Two Sum]] 2026-09-14 | brute force + complexity stated before the optimal solution, unprompted, in 3 consecutive boxes |
| The average-case caveat on hash ops — **1 of 3.** Said "average O(n)" but not the matching worst case, so the sentence is still half-finished | missed [[../problems/contains-duplicate\|Contains Duplicate]] and [[../problems/valid-anagram\|Valid Anagram]] 2026-09-13; **present** [[../problems/two-sum\|Two Sum]] 2026-09-14 | average *and* worst both stated unprompted in 3 consecutive debriefs |
| Constraints not read into the complexity — **1 of 2.** Explained O(min(n,k)) correctly when asked on 2026-09-14, but has not yet volunteered it unprompted | [[../problems/valid-anagram\|Valid Anagram]] 2026-09-13 (missed); [[../problems/two-sum\|Two Sum]] 2026-09-14 (correct, but prompted) | space reasoned against the constraints, unprompted, twice |
| Never names the approach rejected and why — the one process step still missing in all 3 boxes | [[../problems/two-sum\|Two Sum]], 2026-09-14 | an alternative named and rejected with a reason, unprompted, in 2 consecutive boxes |

## Background
*Worth fixing, not urgent.*

| Gap | Discovered via | Closes when |
|---|---|---|
| Hours per week, target date and definition of done still blank in [[profile]], so weekly volume is a guess | wiki setup, 2026-09-10; partially filled 2026-09-14 | those three fields filled in |
| Behavioral and design claimed solid from 4 years of doing the EM job, but `unseen` on the [[skill-tree\|skill tree]] — no linked evidence. Doing the job well and performing it against a loop rubric are different skills | stated by Rob 2026-09-14 | one design post-mortem page written up from real Vivint work |
| Everything outside DSA is parked by choice, **reaffirmed 2026-09-14** after Rob corrected an initial suggestion to rebalance toward design/behavioral. DSA is the self-assessed weakest area and the perishable one | 2026-09-10; reaffirmed 2026-09-14 | Topic 1–3 are cold-solvable |

---

## How gaps get here

The honest ones come from **debriefs and real interviews**, not from
introspection. Every `debrief` that exposes a weakness adds a row, with the
problem write-up as the discovery source. That's the system working.

## Closed gaps

Kept, not deleted — the record of closing them is the record of progress.

| Gap | Closed | Evidence |
|---|---|---|
| DSA untested — no timed attempts on record, no idea where the real ceiling is | 2026-09-14 | All 3 Easies of [[../curriculum/arrays-hashing\|Topic 1]] attempted, debriefed and **solved cold**: [[../problems/contains-duplicate\|Contains Duplicate]] 7 min, [[../problems/valid-anagram\|Valid Anagram]] 7 min, [[../problems/two-sum\|Two Sum]] 11 min. The ceiling on Easies is not the binding constraint; the Mediums will locate the real one |
