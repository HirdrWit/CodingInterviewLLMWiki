---
type: curriculum
status: unseen
updated: 2026-09-14
sources: [https://neetcode.io/practice/practice/blind75]
tags: [curriculum, bit-manipulation]
---

# Topic 18 — Bit Manipulation

**Lesson:** [[../lessons/18-bit-manipulation|Lesson 18 — Bit Manipulation]]
**Skill:** [[../skills/algorithms|Algorithms]]

5 problems, four of them Easy. Cheap points — but the cheapness is conditional
on the hour spent in §3 of the lesson, not on the problems themselves. Each has
a three-line answer **once you know the trick**, and no amount of on-the-spot
cleverness reliably derives these under pressure.

This is the only topic in the list where an integer stops being an atom and the
**representation** becomes the thing you operate on. Be honest that it is also
the **least transferable** topic in the set: bit manipulation is mostly asked as
recall. Learn the six operators until they're reflex, memorise three or four
identities, move on. Do not spend a week here.

Work them in this order. Problems 1 and 2 are the natural pair and can share a
session; 3 forces the fixed-width discipline; 4 is the one genuinely elegant
idea; 5 is the Medium, and the only place carries break column independence.

| # | Problem | Difficulty | Pattern | Attempts | Status | Write-up |
|---|---|---|---|---|---|---|
| 1 | Number of 1 Bits | Easy | `n & (n-1)` clears the lowest set bit | 0 | `unseen` | — |
| 2 | Counting Bits | Easy | DP over bits | 0 | `unseen` | — |
| 3 | Reverse Bits | Easy | shift out one end, shift into the other | 0 | `unseen` | — |
| 4 | Missing Number | Easy | XOR cancellation, or the Gauss sum | 0 | `unseen` | — |
| 5 | Sum of Two Integers | Medium | XOR for the sum, AND-shift for the carry | 0 | `unseen` | — |

The **Pattern** column is filled in here for tracking, but read it *after* a
timed attempt, never before. Knowing "this is an XOR-cancellation problem" going
in removes the exact skill the interview tests: recognising it yourself.

## Concepts

- [[../concepts/bitwise-operators|Bitwise Operators]] — all five; the foundation
- [[../concepts/n-and-n-minus-1|`n & (n - 1)`]] — problem 1
- [[../concepts/xor-cancellation|XOR Cancellation]] — problems 4 and 5
- [[../concepts/twos-complement|Two's Complement]] — problems 3 and 5, and the
  reason a Python solution passes the positive cases and fails the negative ones

## Topic done when

- [ ] All 5 solved at least once
- [ ] All 5 re-solved **cold** (no notes, no hints) on a later day
- [ ] Can write the read / set / clear / toggle expressions for bit `i` from
      memory, without hesitating
- [ ] Can walk `n & (n - 1)` through `n = 44` in bit columns and explain why the
      lowest set bit dies and nothing else moves
- [ ] Can state why `-1` is all ones, and what Python's `>>` does to a negative
- [ ] Can explain what `0xFFFFFFFF` masking does and why it isn't enough alone
- [ ] Can say "O(1), because the width is a fixed 32 bits" **with the reason
      attached**, not as a bare claim

## The list is not the finish line

This is topic 18 of 18. Finishing it means every problem in the Blind 75 has been
**attempted once** — which is the halfway mark, not the end. Per the schema, a
problem counts when it is **re-solved cold on a later day**, and the real
scoreboard is [[../meta/review-queue|the review queue]] emptying, not this table
filling in.

Expect the earliest topics to have gone stale by the time you arrive here. That
is the system working as designed, not backsliding. Run `review`, take the
demotions honestly, and go round again. **Repeat until confident is the default,
not the fallback.**

## Notes

*Accumulates as problems get worked — recurring mistakes, insights that transfer.*
