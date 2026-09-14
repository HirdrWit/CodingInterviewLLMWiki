---
type: curriculum
status: unseen
updated: 2026-09-14
sources: [https://neetcode.io/practice/practice/blind75]
tags: [curriculum, backtracking]
---

# Topic 9 — Backtracking

**Lesson:** [[../lessons/09-backtracking|Lesson 9 — Backtracking]]
**Skill:** [[../skills/algorithms|Algorithms]]

2 problems, both Medium, and they are deliberately the two different faces of
the same template. The first builds an answer in a list; the second builds one
in a grid. If the `choose / explore / un-choose` skeleton is the same in both
of your solutions, you've learned the topic. If they look like unrelated code,
you've learned two problems.

Depends on [[stack|Topic 4 — Stack]] (the call stack is a stack, and backtracking
is what pushing and popping it looks like) and [[trees|Topic 7 — Trees]] (DFS).

| # | Problem | Difficulty | Pattern | Attempts | Status | Write-up |
|---|---|---|---|---|---|---|
| 1 | Combination Sum | Medium | loop-from-index + pruning | 0 | `unseen` | — |
| 2 | Word Search | Medium | grid DFS + mark/unmark | 0 | `unseen` | — |

The **Pattern** column is filled in here for tracking, but read it *after* a
timed attempt, never before. Knowing "this one reuses candidates, so recurse
with `j` not `j+1`" going in removes the exact skill the interview tests:
recognising it yourself.

## Topic done when

- [ ] Both solved at least once
- [ ] Both re-solved **cold** (no notes, no hints) on a later day
- [ ] The choose / explore / un-choose template written from memory, unaided
- [ ] Can state the complexity as *branching factor and depth*, not a guessed
      exponent, and can say why the output size is a lower bound
- [ ] Can explain aloud why `res.append(path[:])` and `res.append(path)` differ,
      and what the wrong one actually produces
- [ ] Can explain why a grid *path* problem needs a temporary mark where island
      counting needs a permanent one

## Notes

*Accumulates as problems get worked — recurring mistakes, insights that transfer.*
