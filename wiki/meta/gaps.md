---
type: meta
updated: 2026-09-13
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
| DSA untested — 2 of the first 3 problems of [[../curriculum/arrays-hashing\|Topic 1]] attempted and debriefed, both solved cold | wiki setup, 2026-09-10; 2 of 3 done 2026-09-13 | Two Sum attempted and debriefed (planned 2026-09-14) |

## Serious
*Will show up soon.*

| Gap | Discovered via | Closes when |
|---|---|---|
| Complexity analysis unproven — stating time *and* space unprompted is a scored behaviour, not a bonus | [[../lessons/01-arrays-hashing\|Lesson 1]], 2026-09-10 | 3 consecutive debriefs where complexity was stated correctly without being asked |
| **Narration under a timer is untrained — now the top DSA risk.** The algorithms are not the constraint — both Easies were solved cold in 7 min. | [[../problems/contains-duplicate\|Contains Duplicate]] 2026-09-13 (silent); [[../problems/valid-anagram\|Valid Anagram]] 2026-09-13 — brute force was requested in writing before the box and skipped anyway | brute force + complexity stated before the optimal solution, unprompted, in 3 consecutive boxes |
| Worst-case complexity of hash operations stated as O(n) rather than O(n²) — the average mistaken for the worst | [[../problems/contains-duplicate\|Contains Duplicate]] 2026-09-13; **missed again** [[../problems/valid-anagram\|Valid Anagram]] 2026-09-13 | "on average" follows "O(1)" unprompted in 3 consecutive debriefs |
| Constraints not read into the complexity — a bounded key space makes a map O(1), not O(n). See [[../concepts/constraint-bounded-complexity]] | [[../problems/valid-anagram\|Valid Anagram]], 2026-09-13 | space stated correctly against the constraints, unprompted, twice |

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
| — | — | — |
