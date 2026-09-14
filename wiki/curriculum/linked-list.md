---
type: curriculum
status: unseen
updated: 2026-09-14
sources: [https://neetcode.io/practice/practice/blind75]
tags: [curriculum, linked-list]
---

# Topic 6 — Linked List

**Lesson:** [[../lessons/06-linked-list|Lesson 6 — Linked List]]
**Skill:** [[../skills/data-structures|Data Structures]]

6 problems. Worked in this order, and the order matters more here than in most
topics: **problems 1 and 2 are the two building blocks, and problems 3, 4 and 6
are made out of them.** Reversal (problem 1) is a sub-step of problem 3.
Dummy-head merging (problem 2) is the whole of problem 6. Make the two Easies
automatic before touching the Mediums — otherwise the Mediums fail for reasons
that have nothing to do with the Mediums.

| # | Problem | Difficulty | Pattern | Attempts | Status | Write-up |
|---|---|---|---|---|---|---|
| 1 | Reverse Linked List | Easy | iterative pointer rewiring (prev/curr/next) | 0 | `unseen` | — |
| 2 | Merge Two Sorted Lists | Easy | dummy head + two-pointer merge | 0 | `unseen` | — |
| 3 | Reorder List | Medium | find middle + reverse second half + interleave | 0 | `unseen` | — |
| 4 | Remove Nth Node From End of List | Medium | offset fast/slow pointers | 0 | `unseen` | — |
| 5 | Linked List Cycle | Easy | Floyd's fast/slow cycle detection | 0 | `unseen` | — |
| 6 | Merge K Sorted Lists | Hard | heap or pairwise merge | 0 | `unseen` | — |

The **Pattern** column is filled in here for tracking, but read it *after* a
timed attempt, never before. Knowing "this is a fast/slow pointer problem" going
in removes the exact skill the interview tests: recognising it yourself.

## Forward dependency on Topic 8

Problem 6 has two valid solutions: a **min-heap** of the k list heads, and
**pairwise merging in rounds**. Heaps are Topic 8 and haven't been covered.
Solve it with the pairwise route — it needs nothing beyond problem 2, and it
reaches the same O(N log k). Mention the heap alternative aloud as the thing
you'd reach for on a stream. Re-solving it with a heap after Topic 8 is a good
review slot, not a prerequisite.

## Topic done when

- [ ] All 6 solved at least once
- [ ] All 6 re-solved **cold** (no notes, no hints) on a later day
- [ ] The three-pointer reversal written correctly first try, from memory, with
      no crash to tell you the line order was wrong
- [ ] Can state time and space complexity for each without thinking, including
      the O(n) stack cost of any recursive version
- [ ] Can explain, from the memory layout alone, why indexing is O(n) here and
      O(1) on an array — and when you would actually choose a linked list

## Notes

*Accumulates as problems get worked — recurring mistakes, insights that transfer.*
