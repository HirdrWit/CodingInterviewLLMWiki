---
type: curriculum
status: unseen
updated: 2026-09-14
sources: [https://neetcode.io/practice/practice/blind75]
tags: [curriculum, 2d-dynamic-programming]
---

# Topic 14 — 2-D Dynamic Programming

**Lesson:** [[../lessons/14-2d-dynamic-programming|Lesson 14 — 2-D Dynamic Programming]]
**Skill:** [[../skills/algorithms|Algorithms]]

Only 2 problems — the smallest topic in the curriculum by count, and one of the
highest-value by skill. Worked in this order deliberately: the grid problem hands
you the state and lets you practise table setup and fill order, then the string
problem makes you *design* a state for the first time.

Topic 13 taught the machinery but let you cheat on the hardest part — the state
was read off the problem statement. This is where that stops.

| # | Problem | Difficulty | Pattern | Attempts | Status | Write-up |
|---|---|---|---|---|---|---|
| 1 | Unique Paths | Medium | grid path counting | 0 | `unseen` | — |
| 2 | Longest Common Subsequence | Medium | two-string DP table | 0 | `unseen` | — |

The **Pattern** column is filled in here for tracking, but read it *after* a
timed attempt, never before. Knowing "this is a two-string prefix table" going in
removes the exact skill the interview tests: recognising it yourself. With only
two problems here there are only two chances to practise that recognition —
don't spend one of them on a spoiler.

## Concepts this topic introduces

- [[../concepts/dp-grid]] — 2-D state over a literal grid; counting and costing paths
- [[../concepts/subsequence-dp]] — the two-string prefix table, the match/no-match
  branch, and the +1 row and column

Assumed from Topic 13: [[../concepts/dynamic-programming]],
[[../concepts/state-transition]], [[../concepts/memoization]],
[[../concepts/bottom-up-tabulation]], [[../concepts/rolling-variables]].

## Topic done when

- [ ] Both solved at least once
- [ ] Both re-solved **cold** (no notes, no hints) on a later day
- [ ] Can hand-fill a 4×4 two-string table from memory, saying what each cell means
- [ ] Can state what `dp[i][j]` means — including whether `i` is an index or a
      count — *before* allocating anything, without being prompted
- [ ] Can say which neighbour the match branch reads and why it's that one
- [ ] Always allocates with a comprehension, never `[[0] * n] * m`
- [ ] States the exponential brute force and why the table removes the exponent,
      before writing the table
- [ ] Volunteers the row-rolling optimisation, says O(min(m, n)) rather than
      O(n), and names what it costs (no reconstruction)
- [ ] Checks the degenerate inputs — 1×1 grid, 1×n grid, one or both strings empty

## Notes

*Accumulates as problems get worked — recurring mistakes, insights that transfer.*
