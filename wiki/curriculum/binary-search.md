---
type: curriculum
status: unseen
updated: 2026-09-14
sources: [https://neetcode.io/practice/practice/blind75]
tags: [curriculum, binary-search]
---

# Topic 5 — Binary Search

**Lesson:** [[../lessons/05-binary-search|Lesson 5 — Binary Search]]
**Skill:** [[../skills/algorithms|Algorithms]]

2 problems, both Medium, and they are the same problem twice — the second one
adds a case analysis on top of the first one's machinery. Small topic, high
leverage: the template you build here gets reused in Heap, Intervals, and every
"minimise the maximum" question for the rest of the curriculum.

Do problem 1 first and do not skip to problem 2 until your template is something
you can write from memory without hesitating on the `<` versus `<=`.

| # | Problem | Difficulty | Pattern | Attempts | Status | Write-up |
|---|---|---|---|---|---|---|
| 1 | Find Minimum in Rotated Sorted Array | Medium | broken invariant \| boundary search | 0 | `unseen` | — |
| 2 | Search in Rotated Sorted Array | Medium | broken invariant \| identify the sorted half | 0 | `unseen` | — |

The **Pattern** column is filled in here for tracking, but read it *after* a
timed attempt, never before. Knowing "this is a broken-invariant problem" going
in removes the exact skill the interview tests: recognising it yourself.

## Topic done when

- [ ] Both problems solved at least once
- [ ] Both re-solved **cold** (no notes, no hints) on a later day
- [ ] The `while lo < hi` template written from memory, correctly, twice on
      different days — including the `mid = lo + (hi - lo) // 2` form
- [ ] Can state what `lo` and `hi` mean at every point in the loop, aloud
- [ ] Can explain why `lo = mid` loops forever, and both fixes
- [ ] Can hand-trace `n = 0`, `n = 1`, `n = 2` without running the code
- [ ] Can recognise a Pattern B "binary search on the answer" problem when
      nothing in it is sorted

## Notes

*Accumulates as problems get worked — recurring mistakes, insights that transfer.*
