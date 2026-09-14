---
type: curriculum
status: unseen
updated: 2026-09-14
sources: [https://neetcode.io/practice/practice/blind75]
tags: [curriculum, stack]
---

# Topic 4 — Stack

**Lesson:** [[../lessons/04-stack|Lesson 4 — Stack]]
**Skill:** [[../skills/data-structures|Data Structures]]

1 problem. The smallest topic on the roadmap by problem count, and the one most
easily under-served because of it. The stack itself is fifteen lines; what earns
the topic its place is that it is the primitive behind iterative tree traversal
([[index|Topic 7]]), backtracking ([[index|Topic 9]]) and DFS
([[index|Topic 11]]) — none of which will announce themselves as stack problems.

| # | Problem | Difficulty | Pattern | Attempts | Status | Write-up |
|---|---|---|---|---|---|---|
| 1 | Valid Parentheses | Easy | LIFO matching | 0 | `unseen` | — |

The **Pattern** column is filled in here for tracking, but read it *after* a
timed attempt, never before. Knowing "this is a LIFO matching problem" going in
removes the exact skill the interview tests: recognising it yourself.

## Beyond the one problem

Because a single Easy cannot demonstrate the topic, two extra exercises count
toward it being done. They are set out at the end of
[[../lessons/04-stack|Lesson 4]]:

- **Daily Temperatures** — the [[../concepts/monotonic-stack|monotonic stack]],
  which no Blind 75 problem in this topic drills but which is the standard
  follow-up.
- **Recursive → iterative rewrite** — sum a nested list recursively, then again
  with an explicit stack. This is Topic 7's groundwork, done early.

## Topic done when

- [ ] Valid Parentheses solved at least once
- [ ] Valid Parentheses re-solved **cold** (no notes, no hints) on a later day
- [ ] Both failure modes named unprompted — a bad close, and leftovers at the end
- [ ] A monotonic stack written from scratch, with the O(n) amortization argument
      stated aloud
- [ ] One recursion converted to an explicit stack, unaided
- [ ] Can state why `pop()` is O(1) and `pop(0)` is O(n) without thinking

## Notes

*Accumulates as problems get worked — recurring mistakes, insights that transfer.*
