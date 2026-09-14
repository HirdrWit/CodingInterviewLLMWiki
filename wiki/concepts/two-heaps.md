---
type: concept
status: unseen
updated: 2026-09-14
sources: []
tags: [heap, priority-queue, two-heaps]
---

# Two Heaps

**In one sentence, in my own words** — split a collection into a low half and a
high half, hold the low half in a max-heap and the high half in a min-heap so
the two roots sit face to face at the boundary, and keep the sizes balanced —
turning "what's in the middle?" into two O(1) peeks.

Pattern C in [[../lessons/08-heap|Lesson 8]].

## When to reach for it

Trigger: "median", "the middle element", "balance two halves", "the value that
splits the data" — **and the data arrives over time** rather than all at once.

The reasoning that gets you there: one [[heap]] gives you one **extreme**. A
median is not an extreme, it is a **boundary**. But a boundary is just the
meeting point of the top of one half and the bottom of the other — and each of
*those* is an extreme. So: two heaps, pointed at each other.

The "arrives over time" half matters. If you get the whole array at once and are
asked once, sort it and index the middle — O(n log n) and done. Two heaps earn
their complexity when insertions keep coming and the answer is asked repeatedly.

## How it works

```
        low half                          high half
    MAX-heap (biggest on top)        MIN-heap (smallest on top)

        ┌───┐                              ┌───┐
        │ 4 │ ← largest of the small       │ 5 │ ← smallest of the large
      ┌─┴─┬─┴─┐                          ┌─┴─┬─┴─┐
      │ 2 │ 3 │                          │ 7 │ 6 │
      └───┴───┘                          └───┴───┘

            └──────── the boundary is HERE ─────────┘
                    both roots are O(1) to read
```

Two invariants, and every bug in this pattern is one of them breaking:

1. **Ordering** — every element of the low half ≤ every element of the high
   half. (Equivalently: `max(low) ≤ min(high)`, which is exactly the two roots.)
2. **Balance** — the two sizes differ by at most one. Pick a convention for
   which side is allowed to be bigger and never deviate from it.

The shape of every insertion is the same: **push somewhere, then repair.** Push
onto one side, then move the offending root across if invariant 1 broke, then
move a root across if invariant 2 broke. Don't try to decide the correct
destination up front by cleverness — push and repair is shorter and provably
correct.

In Python, the max-heap half is a `heapq` min-heap of **negated** values. That
negation, applied in both directions, is where the bugs live.

## Minimal example

The invariant, not an implementation — deriving the repair steps is the exercise
of [[../curriculum/heap|Topic 8]]:

```python
low  = []   # MAX-heap, stored negated: -low[0] is the largest of the small half
high = []   # MIN-heap:                  high[0] is the smallest of the large half

# after every insertion, both of these must hold:
#   1.  -low[0] <= high[0]                       (ordering)
#   2.  abs(len(low) - len(high)) <= 1           (balance)
#
# then the boundary value is read in O(1) from the roots alone:
#   sizes unequal  ->  the root of the bigger heap
#   sizes equal    ->  combine the two roots
```

## Complexity

- **Insert: O(log n)** — a constant number of pushes and pops, each O(log n).
- **Query the boundary: O(1)** — two array reads, no traversal.
- **Space: O(n)** — every element is in exactly one of the two heaps.

Compare with the honest baselines: an unsorted list is O(1) insert but O(n log n)
per query; a sorted list is O(log n) to *find* the insertion point but **O(n)**
to actually insert, because everything shifts. Two heaps beat both when inserts
and queries are interleaved. Say the sorted-list baseline out loud before
reaching for this — it is a legitimate working answer and a good place to start.

## Gotchas

- **Repair in the right order.** Fix ordering first, then balance. Doing balance
  first can leave an element on the wrong side.
- **The negation, both directions.** Push `-x` onto `low`, and read `-low[0]`.
  Half of all two-heap bugs are one missing minus sign.
- **Moving an element across is pop-then-push**, and the sign flips on the way.
- **The empty case.** The very first insertion has no roots to compare against.
  Guard it, or push unconditionally onto a designated side and let the repair
  step sort it out.
- **Even vs odd totals** are different answers. Decide the convention (which
  heap may hold the extra element) before writing code, and write it down.
- **Don't reach for this when one sort would do.** No stream, one query, no
  insertions? Sort.

## Related

- [[heap]] — the component. Read that first; this is two of them with an
  invariant between.
- [[two-pointers]] — the other "two things converging on a boundary" idea, but
  from opposite ends of a **sorted array** and in O(1) space. Two heaps work on
  unsorted data arriving over time; two pointers need the order up front.

## Evidence

Problems where I used this unaided:

*Nothing yet — [[../curriculum/heap|Topic 8]] not started.*
