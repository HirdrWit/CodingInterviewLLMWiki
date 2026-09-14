---
type: concept
status: unseen
updated: 2026-09-14
sources: [https://docs.python.org/3/library/heapq.html, https://www.geeksforgeeks.org/binary-heap/]
tags: [heap, priority-queue, data-structures]
---

# Heap (Priority Queue)

**In one sentence, in my own words** — a half-sorted array that always knows its
smallest (or largest) element for free, and can give it up or take a new element
in O(log n), because it only ever maintains order *vertically* — parent before
child — and never between siblings.

Pattern A in [[../lessons/08-heap|Lesson 8]]. The structure you reach for when
you need the extreme **repeatedly**, from a collection that keeps changing.

## When to reach for it

Trigger: "repeatedly take the largest/smallest", "process in priority order",
"the k largest / k closest / kth largest", "merge k sorted sources", "until one
remains", "shortest path with weights".

The tell is two words together: **repeatedly** and **changing**. A brute force
whose inner loop exists only to *find the max*, inside an outer loop that
*mutates the collection*, is a heap problem.

**The decision rule:**

> Need the whole order, once? **Sort** — O(n log n), less code, easier to defend.
> Need the extreme, repeatedly, from a changing collection? **Heap.**

A heap that gets built and fully drained exactly once bought you nothing.

## How it works

Two rules, and everything follows from them:

1. **Heap property** — every node is ≤ (min-heap) or ≥ (max-heap) both of its
   children. Nothing is said about siblings. The root is the extreme.
2. **Complete tree** — every level full except the last, which fills left to
   right.

Rule 2 is what lets the tree live in a flat array with no pointers:

```
left(i) = 2i + 1      right(i) = 2i + 2      parent(i) = (i - 1) // 2
```

- **push** — append at the end, then **sift up**: swap with the parent while it
  outranks the parent. Travels at most the height.
- **pop** — take the root, move the *last* element into the root slot, shrink,
  then **sift down**: swap with the **better of the two children** (the smaller
  one in a min-heap) until it fits. Swapping with the wrong child is the classic
  bug.
- **heapify** — sift down every node from the last parent back to index 0.

## Minimal example

```python
import heapq

heap = []
heapq.heappush(heap, 5)          # O(log n)
smallest = heap[0]               # O(1) peek — just indexing
smallest = heapq.heappop(heap)   # O(log n)

nums = [5, 1, 9, 3]
heapq.heapify(nums)              # O(n), in place, returns None
```

`heapq` is **min-only**. For a max-heap, negate going in and coming back out:

```python
heapq.heappush(heap, -x)
largest = -heapq.heappop(heap)
```

The k-largest idiom — the most reusable thing in the topic:

```python
def k_largest(nums, k):              # O(n log k) time, O(k) space
    heap = []                        # a MIN-heap: its root is the weakest
    for x in nums:                   # survivor, i.e. the one to evict
        heapq.heappush(heap, x)
        if len(heap) > k:
            heapq.heappop(heap)
    return heap
```

## Complexity

| Operation | Cost | Why |
|---|---|---|
| peek | **O(1)** | The extreme is at index 0. Nothing to compute. |
| push | **O(log n)** | Sift-up is bounded by the height, ⌊log₂ n⌋. |
| pop | **O(log n)** | Sift-down, same bound. |
| heapify | **O(n)** | See below. |
| search / arbitrary delete | **O(n)** | No horizontal order — it's just an array. |
| space | **O(n)** | Flat list, no per-node pointers. |

**Why heapify is O(n), not O(n log n):** the cost of sifting a node down is
*that node's* height, not the tree's. Half the nodes are leaves and cost zero, a
quarter cost one swap, an eighth cost two. The sum `n · Σ h/2^h` converges,
giving O(n). The work is concentrated where the nodes are rare.

That does **not** make heapsort beat sorting: build O(n) + n pops at O(log n) is
still O(n log n).

## Gotchas

- **The list is not sorted.** `heap[0]` is the minimum; `heap[1]` is not the
  second smallest. Never iterate a heap expecting order.
- **`heapq` has no `max=True`.** Negate — and remember to negate *back*.
- **Negate the key only**, not the payload: `(-count, word)`, never `(-count, -word)`.
- **Tie comparison crashes.** Tuples compare elementwise, so equal priorities
  make Python compare the payloads. Insert a monotonic counter:
  `(priority, i, obj)`.
- **`heapreplace` errors on an empty heap** and always returns the old root even
  if the new element is smaller. `heappushpop` is the safe one.
- **A heap is not a search structure.** "Remove this specific element" is O(n).
  If the problem needs that, you want a BST, a sorted container, or a heap plus
  lazy deletion.
- **A max-heap for "k largest" is backwards** — it's O(n log n) and O(n) space,
  and it can't run on a stream.
- **`2i` / `2i+1`** is the 1-indexed form from older textbooks. In Python,
  0-indexed: `2i+1` / `2i+2`.

## Related

- [[two-heaps]] — two of these facing each other, for when the thing you want is
  the *boundary* between halves rather than an extreme
- [[hash-map]] — the other memory-for-time trade: hash maps buy O(1)
  *membership*, heaps buy O(1) *extreme*. Neither gives you order cheaply.
- [[frequency-map]] — usually the first half of a top-k problem; the heap is the
  second half

## Evidence

Problems where I used this unaided:

*Nothing yet — [[../curriculum/heap|Topic 8]] not started.*
