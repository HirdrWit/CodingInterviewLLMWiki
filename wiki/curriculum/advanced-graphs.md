---
type: curriculum
status: unseen
updated: 2026-09-14
sources: [https://neetcode.io/practice/practice/blind75]
tags: [curriculum, advanced-graphs]
---

# Topic 12 — Advanced Graphs

**Lesson:** [[../lessons/12-advanced-graphs|Lesson 12 — Advanced Graphs]]
**Skill:** [[../skills/algorithms|Algorithms]]
**Builds on:** [[graphs|Topic 11 — Graphs]] (traversals, adjacency lists,
topological sort) and [[heap|Topic 8 — Heap / Priority Queue]].

One problem, and it's a Hard. The thin problem count is misleading: the
algorithms that live in this topic — Dijkstra, union-find, MST — are asked about
in real interviews far more often than the Blind 75's single entry suggests.
Lesson 12 covers them at recognition depth, explicitly flagged as beyond the
list. Don't turn them into timed problems yet; the list comes first.

| # | Problem | Difficulty | Pattern | Attempts | Status | Write-up |
|---|---|---|---|---|---|---|
| 1 | Alien Dictionary | Hard | implicit graph → topological sort | 0 | `unseen` | — |

The **Pattern** column is filled in here for tracking, but read it *after* a
timed attempt, never before. Knowing "this is an implicit-graph problem" going
in removes the exact skill the interview tests: noticing the graph yourself.

## Topic done when

- [ ] Alien Dictionary solved at least once
- [ ] Alien Dictionary re-solved **cold** (no notes, no hints) on a later day
- [ ] Can derive the graph from the input out loud, without code, in under two
      minutes — nodes, edges, direction
- [ ] Can explain why a two-state `visited` set is insufficient for directed
      cycle detection, with the diamond counterexample
- [ ] Can state Kahn's vs DFS topological sort and justify the choice
- [ ] Can name the trigger and the complexity for Dijkstra, union-find and MST
      without implementing them

## Notes

*Accumulates as problems get worked — recurring mistakes, insights that transfer.*
