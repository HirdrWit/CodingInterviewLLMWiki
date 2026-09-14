---
type: curriculum
status: unseen
updated: 2026-09-14
sources: [https://neetcode.io/practice/practice/blind75]
tags: [curriculum, 1d-dynamic-programming]
---

# Topic 13 — 1-D Dynamic Programming

**Lesson:** [[../lessons/13-1d-dynamic-programming|Lesson 13 — 1-D Dynamic Programming]]
**Skill:** [[../skills/algorithms|Algorithms]]

10 problems — the second-biggest topic in the curriculum, and the one where most
people stall. Worked in this order: problem 1 is the smallest complete DP that
exists, and each one after it changes exactly one thing about the state.

Do not skip the Easy. The point of Climbing Stairs is not the answer — it's
running the four-question procedure on a problem simple enough that you can see
yourself doing it.

| # | Problem | Difficulty | Pattern | Attempts | Status | Write-up |
|---|---|---|---|---|---|---|
| 1 | Climbing Stairs | Easy | Fibonacci recurrence | 0 | `unseen` | — |
| 2 | House Robber | Medium | take-or-skip with two rolling variables | 0 | `unseen` | — |
| 3 | House Robber II | Medium | circular constraint, run the line case twice | 0 | `unseen` | — |
| 4 | Longest Palindromic Substring | Medium | expand around centre | 0 | `unseen` | — |
| 5 | Palindromic Substrings | Medium | expand around centre, counting | 0 | `unseen` | — |
| 6 | Decode Ways | Medium | recurrence with a validity check on 1- and 2-digit reads | 0 | `unseen` | — |
| 7 | Coin Change | Medium | unbounded knapsack over amounts | 0 | `unseen` | — |
| 8 | Maximum Product Subarray | Medium | track running max AND min | 0 | `unseen` | — |
| 9 | Word Break | Medium | reachability over string positions | 0 | `unseen` | — |
| 10 | Longest Increasing Subsequence | Medium | O(n²) DP; O(n log n) patience/binary-search variant | 0 | `unseen` | — |

The **Pattern** column is filled in here for tracking, but read it *after* a
timed attempt, never before. Knowing "this is an unbounded knapsack" going in
removes the exact skill the interview tests: recognising it yourself. In this
topic that matters more than anywhere else — the patterns rhyme, and the only
durable skill is deriving the state rather than recalling it.

## Concepts this topic introduces

- [[../concepts/dynamic-programming]] — recursion over overlapping subproblems, plus a memory
- [[../concepts/memoization]] — the top-down whiteboard
- [[../concepts/bottom-up-tabulation]] — the same recurrence, filled forward
- [[../concepts/state-transition]] — the four questions, and why the state definition is the solve
- [[../concepts/rolling-variables]] — dropping the table when the recurrence reaches back k steps

## Topic done when

- [ ] All 10 solved at least once
- [ ] All 10 re-solved **cold** (no notes, no hints) on a later day
- [ ] The four questions (state / recurrence / base cases / fill order) are
      written down *before* code on every attempt, without being prompted
- [ ] Can state time and space complexity for each as *states × work per state*,
      without thinking
- [ ] Can convert any of them between top-down memo and bottom-up table on demand
- [ ] Can space-optimise to rolling variables wherever the recurrence permits,
      and say why it does
- [ ] Can explain to a rubber duck why DP applies — naming overlapping
      subproblems and optimal substructure — and give one problem where it doesn't

## Notes

*Accumulates as problems get worked — recurring mistakes, insights that transfer.*
