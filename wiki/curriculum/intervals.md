---
type: curriculum
status: unseen
updated: 2026-09-14
sources: [https://neetcode.io/practice/practice/blind75]
tags: [curriculum, intervals]
---

# Topic 16 — Intervals

**Lesson:** [[../lessons/16-intervals|Lesson 16 — Intervals]]
**Skill:** [[../skills/algorithms|Algorithms]]

5 problems. Small topic, one hard idea: **the sort key is the algorithm.** Work
them in this order — problem 3 deliberately follows problem 2 so that the
instinct built in 2 (sort by start) gets caught being wrong. That collision is
the lesson; don't skip past it.

Depends on [[../lessons/08-heap|Topic 8 — Heap]] for problem 5, and on
[[../lessons/15-greedy|Topic 15 — Greedy]] for problem 3.

| # | Problem | Difficulty | Pattern | Attempts | Status | Write-up |
|---|---|---|---|---|---|---|
| 1 | Insert Interval | Medium | three-phase scan (before / overlapping / after) | 0 | `unseen` | — |
| 2 | Merge Intervals | Medium | sort by start, then absorb | 0 | `unseen` | — |
| 3 | Non-overlapping Intervals | Medium | sort by END, greedy keep | 0 | `unseen` | — |
| 4 | Meeting Rooms | Easy | sort by start, check adjacent pairs | 0 | `unseen` | — |
| 5 | Meeting Rooms II | Medium | min-heap of end times, or a sweep over start/end events | 0 | `unseen` | — |

The **Pattern** column is filled in here for tracking, but read it *after* a
timed attempt, never before. Knowing "this one sorts by end" going in removes
the exact skill the interview tests: working out the sort key yourself.

## Topic done when

- [ ] All 5 solved at least once
- [ ] All 5 re-solved **cold** (no notes, no hints) on a later day
- [ ] Can write the overlap test correctly *and derive it from the two disjoint
      cases* without hesitating
- [ ] Can say, unprompted, why "remove the fewest" sorts by end and not by
      start — including the exchange argument
- [ ] Can give both the heap and the sweep-line solution to the room-count shape
- [ ] Asks the inclusive-vs-exclusive endpoint question before writing code

## Notes

*Accumulates as problems get worked — recurring mistakes, insights that transfer.*
