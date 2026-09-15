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
| Complexity analysis unproven — stating time *and* space unprompted is a scored behaviour, not a bonus | [[../lessons/01-arrays-hashing\|Lesson 1]], 2026-09-10 | 3 consecutive debriefs where complexity was stated correctly without being asked |
| Narration under a timer — **1 of 3 consecutive boxes done.** Still top DSA risk until it holds under a Medium, where the pressure is real | [[../problems/contains-duplicate\|Contains Duplicate]] 2026-09-13 (silent); [[../problems/valid-anagram\|Valid Anagram]] 2026-09-13 (skipped when asked); **done** [[../problems/two-sum\|Two Sum]] 2026-09-14 | brute force + complexity stated before the optimal solution, unprompted, in 3 consecutive boxes |
| The average-case caveat on hash ops — **1 of 3.** Said "average O(n)" but not the matching worst case, so the sentence is still half-finished | missed [[../problems/contains-duplicate\|Contains Duplicate]] and [[../problems/valid-anagram\|Valid Anagram]] 2026-09-13; **present** [[../problems/two-sum\|Two Sum]] 2026-09-14 | average *and* worst both stated unprompted in 3 consecutive debriefs |
| Constraints not read into the complexity — **1 of 2.** Explained O(min(n,k)) correctly when asked on 2026-09-14, but has not yet volunteered it unprompted | [[../problems/valid-anagram\|Valid Anagram]] 2026-09-13 (missed); [[../problems/two-sum\|Two Sum]] 2026-09-14 (correct, but prompted) | space reasoned against the constraints, unprompted, twice |
| Never names the approach rejected and why — the one process step still missing in all 3 boxes | [[../problems/two-sum\|Two Sum]], 2026-09-14 | an alternative named and rejected with a reason, unprompted, in 2 consecutive boxes |

## Background
*Worth fixing, not urgent.*

| Gap | Discovered via | Closes when |
|---|---|---|
| [[profile]] unset — no target role, so weekly volume is guesswork | wiki setup, 2026-09-10 | [[profile]] filled in |
| Everything outside DSA is parked by choice — see [[current-plan\|current plan]] | 2026-09-10 | Topic 1–3 are cold-solvable |

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
