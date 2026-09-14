---
type: curriculum
status: unseen
updated: 2026-09-14
sources: [https://neetcode.io/practice/practice/blind75]
tags: [curriculum, sliding-window]
---

# Topic 3 — Sliding Window

**Lesson:** [[../lessons/03-sliding-window|Lesson 3 — Sliding Window]]
**Skill:** [[../skills/algorithms|Algorithms]]

4 problems, and the difficulty curve here is the steepest in the first half of
the list: one Easy, two Mediums, then the topic's Hard. Worked in this order —
each one adds exactly one thing to the window's state. Problem 1 carries no
state at all beyond a single number; problem 4 carries two counting structures
and an integer keeping them in sync. Skipping ahead means meeting all of that at
once.

| # | Problem | Difficulty | Pattern | Attempts | Status | Write-up |
|---|---|---|---|---|---|---|
| 1 | Best Time to Buy and Sell Stock | Easy | running minimum / one-pass scan | 0 | `unseen` | — |
| 2 | Longest Substring Without Repeating Characters | Medium | variable window + seen-set | 0 | `unseen` | — |
| 3 | Longest Repeating Character Replacement | Medium | variable window + frequency map + max-count invariant | 0 | `unseen` | — |
| 4 | Minimum Window Substring | Hard | variable window + need/have counting | 0 | `unseen` | — |

The **Pattern** column is filled in here for tracking, but read it *after* a
timed attempt, never before. Knowing "this is a need/have counting problem"
going in removes the exact skill the interview tests: recognising it yourself.

## What this topic leans on

- [[arrays-hashing|Topic 1]] — the window's state is almost always a
  [[../concepts/frequency-map|frequency map]] or a [[../concepts/seen-set|seen-set]]
- [[two-pointers|Topic 2]] — same two indices, moving the same direction, with
  the *range between them* as the object of interest rather than the pair at the
  ends

## Topic done when

- [ ] All 4 solved at least once
- [ ] All 4 re-solved **cold** (no notes, no hints) on a later day
- [ ] Can write the grow/shrink skeleton from memory without thinking
- [ ] Can say, for any given problem, whether the answer is recorded *inside* or
      *after* the shrink loop — and why
- [ ] Can justify O(n) despite the nested loop, using the amortized argument,
      unprompted
- [ ] Can explain to a rubber duck when sliding window is the *wrong* tool
      (negative values, non-contiguous selections, sorted-pair problems)

## Notes

*Accumulates as problems get worked — recurring mistakes, insights that transfer.*
