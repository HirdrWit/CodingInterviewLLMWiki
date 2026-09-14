---
type: curriculum
status: unseen
updated: 2026-09-14
sources: [https://neetcode.io/practice/practice/blind75]
tags: [curriculum, greedy]
---

# Topic 15 — Greedy

**Lesson:** [[../lessons/15-greedy|Lesson 15 — Greedy]]
**Skill:** [[../skills/algorithms|Algorithms]]

2 problems. Both Medium, both one-pass scans with a constant number of running
variables. The code is short; the *justification* is the work. Do not start until
[[1d-dynamic-programming|Topic 13]] is behind you — this topic is defined by
contrast with it, and the coin-change counterexample only lands if you've felt
why DP was needed there.

| # | Problem | Difficulty | Pattern | Attempts | Status | Write-up |
|---|---|---|---|---|---|---|
| 1 | Maximum Subarray | Medium | Kadane — reset the running sum when it turns against you | 0 | `unseen` | — |
| 2 | Jump Game | Medium | reachability frontier — carry the furthest reachable index | 0 | `unseen` | — |

The **Pattern** column is filled in here for tracking, but read it *after* a
timed attempt, never before. Knowing "this is Kadane" going in removes the exact
skill the interview tests: recognising it yourself.

## Topic done when

- [ ] Both solved at least once
- [ ] Both re-solved **cold** (no notes, no hints) on a later day
- [ ] Can state time and space complexity for each without thinking
- [ ] Can state the greedy claim for each in one sentence — *why* the local
      choice is safe, not just what it is
- [ ] Can give the coin-change counterexample from memory, and say why Coin
      Change is a DP problem and these two are not
- [ ] Can explain Kadane as a DP *and* as a greedy, and say where the two
      readings meet

## Notes

*Accumulates as problems get worked — recurring mistakes, insights that transfer.*
