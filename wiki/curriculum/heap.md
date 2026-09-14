---
type: curriculum
status: unseen
updated: 2026-09-14
sources: [https://neetcode.io/practice/practice/blind75]
tags: [curriculum, heap, priority-queue]
---

# Topic 8 — Heap / Priority Queue

**Lesson:** [[../lessons/08-heap|Lesson 8 — Heap / Priority Queue]]
**Skill:** [[../skills/data-structures|Data Structures]]

1 problem — and it is a **Hard**. The smallest topic in the curriculum by count
and one of the most leveraged by usage: the heap is the engine inside Top K
Frequent Elements (Topic 1), Merge K Sorted Lists (Topic 6), and Dijkstra
(Topic 12). Learn it properly here, where there is only one problem in the way.

| # | Problem | Difficulty | Pattern | Attempts | Status | Write-up |
|---|---|---|---|---|---|---|
| 1 | Find Median from Data Stream | Hard | [[../concepts/two-heaps\|two heaps]] — max-heap of the low half, min-heap of the high half | 0 | `unseen` | — |

The **Pattern** column is filled in here for tracking, but read it *after* a
timed attempt, never before. Knowing "this is a two-heaps problem" going in
removes the exact skill the interview tests: recognising it yourself.

**Expect not to finish this one in the first box.** It is the first Hard in the
curriculum. An unfinished attempt requeues at +2 days and comes back until it is
solved cold — that is the plan working, not the plan failing.

## Topic done when

- [ ] Find Median from Data Stream solved at least once
- [ ] Re-solved **cold** (no notes, no hints) on a later day
- [ ] Can state the heap property, the `2i+1` / `2i+2` index arithmetic, and the
      cost of push / pop / peek / heapify without thinking
- [ ] Can explain **why heapify is O(n)** and not O(n log n)
- [ ] Can write the k-largest idiom (min-heap of size k, O(n log k)) from memory,
      and say why it is a *min*-heap
- [ ] Can state the decision rule — heap vs sort — in one sentence
- [ ] Can explain to a rubber duck what a heap is **bad** at, and name the
      structure to use instead

## Notes

*Accumulates as problems get worked — recurring mistakes, insights that transfer.*
