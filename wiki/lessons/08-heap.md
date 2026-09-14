---
type: lesson
topic: heap
status: ready
updated: 2026-09-14
sources: [https://docs.python.org/3/library/heapq.html, https://www.geeksforgeeks.org/heap-data-structure/, https://www.geeksforgeeks.org/binary-heap/]
tags: [lesson, heap, priority-queue]
---

# Lesson 8 — Heap / Priority Queue

**Curriculum:** [[../curriculum/heap|Topic 8]] · **Skill:** [[../skills/data-structures|Data Structures]] · **Materials:** [[../meta/resources|Resources]]
**Prerequisites:** [[../curriculum/arrays-hashing|Topic 1]] (arrays, and the memory-for-time trade), **Topic 7 — Trees** (what a binary tree is — though a heap is barely a tree in practice).
**Work through in:** 45–60 minutes.

---

## 1. Why this topic

Topic 8 has **one** Blind 75 problem. That is a lie about its importance.

The honest reason to learn heaps properly is that the heap is **load-bearing
somewhere else in the list**, not here:

- **Top K Frequent Elements** ([[../curriculum/arrays-hashing|Topic 1]], problem 5) —
  the bucket-sort route is the one that topic teaches, but the heap route is the
  one an interviewer will ask you to compare it against.
- **Merge K Sorted Lists** (**Topic 6 — Linked List**) — the clean
  solution is a heap of the k list heads. Without one you are re-scanning k
  lists on every single pop.
- **Dijkstra's shortest path** (**Topic 12 — Advanced Graphs**
  territory, and a routine follow-up in system-design-flavoured rounds) — is
  *defined* in terms of a priority queue. No heap, no Dijkstra.

So this topic is infrastructure. You are learning it once, here, in a quiet
corner with only one problem in the way, so that it is already automatic when it
shows up as a sub-step inside a harder problem three topics later.

The second reason, and the one that actually generalises: **a heap is what you
reach for when you need the extreme element repeatedly, over a changing
collection.** Arrays & Hashing taught you to spend memory to buy time on
*membership*. The heap is the same trade applied to *order* — and it is the
first structure in the list that is deliberately, usefully **only half sorted**.
That idea (pay only for the order you actually need) is worth more than the one
problem.

---

## 2. The mental model

### A heap is a hospital triage queue, not a queue

A normal queue is fair: first in, first out. A triage queue is not. Whoever is
most critical goes next, regardless of when they arrived. New arrivals slot in
by severity. Nobody ever sorts the whole waiting room — that would be wasted
work, because the only question ever asked is **"who's next?"**

That is a heap. It answers one question fast, and is deliberately vague about
everything else.

### The heap property

A **min-heap** is a binary tree where every node is **≤ both of its children**.
That's the entire rule.

```
                 ┌───┐
                 │ 1 │            ← the minimum, always at the root
                 └─┬─┘
          ┌────────┴────────┐
        ┌─┴─┐             ┌─┴─┐
        │ 3 │             │ 2 │   ← 3 sits above 8 and 5. 2 sits above 9.
        └─┬─┘             └─┬─┘     3 > 2 and that is FINE — no rule
      ┌───┴───┐           ┌─┘       relates siblings or cousins.
    ┌─┴─┐   ┌─┴─┐       ┌─┴─┐
    │ 8 │   │ 5 │       │ 9 │
    └───┘   └───┘       └───┘
```

Read that picture twice. The rule is **vertical only**. `3` being larger than
its cousin `2` breaks nothing. This is the difference between a heap and a BST,
and it is the single most common misunderstanding:

> A BST is fully ordered — an in-order walk gives you sorted output.
> A heap is ordered **only along root-to-leaf paths**. There is no cheap way to
> ask a heap "what's the third smallest?" or "is 7 in here?".

**A heap knows its minimum and nothing else.** Everything a heap is good at, and
every limitation it has, falls out of that sentence.

### It is also shaped

Second rule: a heap is a **complete** binary tree — every level full except
possibly the last, which fills left to right. No gaps.

That constraint is not decoration. It is what lets the tree be stored in a flat
array with no pointers at all, which is section 3.

---

## 3. The mechanics

### The array trick

Number the nodes in level order, left to right, starting at 0:

```
            0:1
          ┌──┴──┐
        1:3    2:2
       ┌─┴─┐   ┌┘
     3:8  4:5 5:9

array:   [ 1,  3,  2,  8,  5,  9 ]
index:     0   1   2   3   4   5
```

Because the tree is complete, the parent/child relationships are pure
arithmetic:

```
left child  of i  =  2i + 1
right child of i  =  2i + 2
parent      of i  =  (i - 1) // 2
```

Check it on the picture: index 1 holds `3`; its children sit at indices
`2·1+1 = 3` and `2·1+2 = 4`, which hold `8` and `5` — both larger than `3`, so
the property holds there. Going the other way, index 5 holds `9`, and its parent
is at `(5-1)//2 = 2`, which holds `2`. Correct.

**There is no tree object.** No nodes, no pointers, no allocation per element.
A heap in Python is a plain `list`. This is why heaps are fast in practice
beyond what the big-O suggests: the data is contiguous and cache-friendly, and
walking to a parent is one integer division.

### sift-up (used by push)

Append the new element at the end of the array — the only place that keeps the
tree complete. It's probably in the wrong place, so **swap it upward with its
parent while it is smaller than that parent.**

```
push 0 into [1, 3, 2, 8, 5, 9]

[1, 3, 2, 8, 5, 9, 0]   append at the end
              ↑
 0 < parent 2  → swap
[1, 3, 0, 8, 5, 9, 2]
       ↑
 0 < parent 1  → swap
[0, 3, 1, 8, 5, 9, 2]
 ↑
 at the root. stop.
```

The element travels at most the height of the tree. A complete tree of n nodes
has height ⌊log₂ n⌋. So push is **O(log n)**.

### sift-down (used by pop)

You want the root. But you can't just remove it — that leaves a hole at the top.
So: **take the last element, move it to the root, shrink the array by one, then
swap it downward with its smaller child until it fits.**

```
pop from [0, 3, 1, 8, 5, 9, 2]   → returns 0

[2, 3, 1, 8, 5, 9]      last element (2) moved to the root
 ↑
 children 3 and 1 → smaller is 1 → 2 > 1 → swap
[1, 3, 2, 8, 5, 9]
       ↑
 children 9 → 2 < 9 → stop.
```

Note **swap with the *smaller* child**, not either one. Swapping with the larger
child would put a big value above a small one and break the property on the
other side. This is the classic implementation bug.

Again bounded by the height: pop is **O(log n)**.

### Why peek is O(1)

The minimum is at index 0, by the property. Reading it is `arr[0]`. There is
nothing to do. That asymmetry — **O(1) to look, O(log n) to remove** — is worth
remembering, because a surprising number of problems only ever need to *look*
(at the threshold, the current worst-of-the-best) and pop rarely.

### Why heapify is O(n), not O(n log n)

Given an unsorted array, you can build a heap by sifting **down** every node
from the last parent back to index 0. The lazy analysis says: n nodes × O(log n)
each = O(n log n). That analysis is wrong, and interviewers like this one.

The cost of sifting down a node is its **height**, not the tree's height — and
almost every node is near the bottom, where the height is nearly zero:

```
level      nodes        height of each     work
─────────────────────────────────────────────────
leaves     n/2      ×   0              =   0
one up     n/4      ×   1              =   n/4
two up     n/8      ×   2              =   2n/8
...
root       1        ×   log n          =   log n
```

Summing `n · Σ (h / 2^(h+1))` over all heights, and the series `Σ h/2^h`
converges to 2. Total work is **O(n)**.

Half the nodes are leaves and cost nothing. That's the intuition to say out
loud: *bottom-up heapify is cheap because the work is concentrated where the
nodes are rare.*

**Corollary worth knowing:** `heapify` is O(n) but it does **not** sort.
Building a heap and popping everything out is heapsort: O(n) + n·O(log n) =
O(n log n) — no better than sorting, plus more code. Heaps beat sorting only
when you don't need the whole order.

---

## 4. Complexity

| Operation | Time | Why |
|---|---|---|
| `peek` (min of a min-heap) | **O(1)** | It's at index 0. Nothing to compute. |
| `push` | **O(log n)** | Sift-up travels at most the tree height. |
| `pop` | **O(log n)** | Sift-down travels at most the tree height. |
| `heapify` (build from a list) | **O(n)** | Work is dominated by the leaves, which cost 0. |
| `heappushpop` / `heapreplace` | **O(log n)** | One sift, not two. Cheaper than push-then-pop. |
| Search for an arbitrary value | **O(n)** | No horizontal order. It's just an array. |
| Delete an arbitrary value | **O(n)** | Finding it is the expensive half. |
| Space | **O(n)** | A flat list. No per-node pointer overhead. |

Read the last three rows as the warning: **a heap is not a search structure.**
The instant a problem needs "remove this specific element" or "is x present",
the heap alone is wrong — you want a BST, a sorted structure, or a heap paired
with a lazy-deletion set.

---

## 5. The patterns

Each one is **a trigger in the problem statement → the tool.** The trigger is
the transferable half.

### Pattern A — repeated access to the extreme → [[../concepts/heap|heap]]
> **Trigger:** "repeatedly take the largest/smallest", "process in priority
> order", "until one/none remain", *and the collection changes as you go*

The tell is the word **repeatedly** combined with **mutation**. If you take the
max, and then the collection changes (something removed, something added), and
then you need the max again — sorting is the wrong tool, because you'd have to
re-sort after every change.

The decision rule, and know this cold:

> **Need the whole order, once? Sort.** O(n log n), done, simpler code.
> **Need the extreme, repeatedly, from a changing collection? Heap.**

A scheduler, a simulation, a merge of k streams, Dijkstra — all the same shape.

### Pattern B — the k-largest idiom (the most reusable thing here)
> **Trigger:** "the k largest", "the k closest", "top k", "the kth largest"

The naive move is to sort and slice: O(n log n). The better move inverts what
you'd expect:

**To keep the k *largest* elements, maintain a *min*-heap of size k.**

```python
import heapq

def k_largest(nums, k):
    heap = []                        # min-heap holding the best k so far
    for x in nums:
        heapq.heappush(heap, x)
        if len(heap) > k:
            heapq.heappop(heap)      # evict the smallest of the k+1
    return heap                      # heap[0] is the kth largest
```

Why a *min*-heap for the *largest* k: the root is the **weakest survivor** — the
one to throw away when a better candidate arrives. You need cheap access to the
worst of your winners, not the best of them.

**Complexity: O(n log k), space O(k).** When k is small and n is huge, that is a
serious win over O(n log n) — and, crucially, it works on a **stream**, where n
isn't known and the data doesn't fit in memory. That last property is why this
idiom shows up in system-design conversations too.

The tighter version of the loop, one sift instead of two:

```python
if len(heap) < k:
    heapq.heappush(heap, x)
elif x > heap[0]:                    # O(1) peek — skip the work entirely
    heapq.heapreplace(heap, x)
```

### Pattern C — two heaps, back to back → [[../concepts/two-heaps|two heaps]]
> **Trigger:** "median", "the middle element", "balance two halves",
> **and the data arrives over time** rather than all at once

One heap gives you one extreme. The median is not an extreme — it is the
*boundary between two halves*. So use two heaps facing each other:

```
        low half                          high half
    MAX-heap (biggest on top)         MIN-heap (smallest on top)

        ┌───┐                              ┌───┐
        │ 4 │ ← largest of the small      │ 5 │ ← smallest of the large
      ┌─┴─┬─┴─┐                          ┌─┴─┬─┴─┐
      │ 2 │ 3 │                          │ 7 │ 6 │
      └───┴───┘                          └───┴───┘

            └──────── the median lives HERE ────────┘
              between the two roots, both O(1) to read
```

Keep the two sizes within one of each other. Then the median is either the root
of the bigger heap (odd total) or the average of the two roots (even total) —
**O(1) to read**, O(log n) to insert. The whole craft is in the invariant: every
element of the low half ≤ every element of the high half, and the sizes stay
balanced. Push, then rebalance; don't try to decide where an element goes by
cleverness alone.

This is the pattern behind the one Blind 75 problem in this topic. Don't look up
the rebalancing rule before your timed attempt — deriving it *is* the exercise.

### Pattern D — merging k sorted sources
> **Trigger:** "merge k sorted lists/arrays/streams", "k-way merge"

Put the head of each of the k sources in a min-heap. Pop the global minimum,
emit it, push that source's next element. The heap holds at most k things, so
each of the n total elements costs O(log k): **O(n log k)** overall.

You'll meet this in **Topic 6 — Linked List**. Recognise it now so
that when you get there the only new part is the pointer plumbing.

---

## 6. Python notes

`heapq` does not give you a heap *object*. It gives you functions that operate
on an ordinary `list` you own. The list *is* the heap.

```python
import heapq

heap = []
heapq.heappush(heap, 5)          # O(log n)
smallest = heap[0]               # O(1) peek — plain indexing, no function
smallest = heapq.heappop(heap)   # O(log n), removes and returns the root

nums = [5, 1, 9, 3]
heapq.heapify(nums)              # O(n), IN PLACE, returns None
```

**`heapq` is a min-heap. Only. There is no `max=True`.** For a max-heap, negate
on the way in and on the way out:

```python
heapq.heappush(heap, -x)         # store negated
largest = -heap[0]               # un-negate on the way out
largest = -heapq.heappop(heap)
```

For tuples, negate only the sort key: `(-count, word)`.

The combined operations, each one sift instead of two:

```python
heapq.heappushpop(heap, x)   # push then pop. Safe on an empty heap.
heapq.heapreplace(heap, x)   # pop then push. FASTER, but ERRORS if empty,
                             # and always returns the old root even if x is smaller.
```

And the convenience wrappers:

```python
heapq.nlargest(k, nums)                      # O(n log k)
heapq.nsmallest(k, nums)
heapq.nlargest(k, counts, key=counts.get)    # key= works like sorted()
```

`nlargest`/`nsmallest` are the right answer in production and fine to *mention*
in an interview — but write the explicit heap loop when the question is testing
whether you understand the idiom. Saying "in real code I'd call `nlargest`,
which does exactly this in O(n log k) — here's the loop it runs" gets you both.

**Two footguns:**

```python
heapq.heappush(heap, (priority, obj))   # ties compare obj — TypeError if obj
                                        # isn't comparable. Add a counter:
heapq.heappush(heap, (priority, i, obj))
```

```python
sorted(heap)         # heap[0] is the min, but heap[1:] IS NOT SORTED.
                     # Never assume iteration order.
```

---

## 7. Traps

- **Assuming the heap list is sorted.** `heap[0]` is the minimum. `heap[1]` is
  *not* the second smallest — printing a heap and reading it as a sorted list is
  how people convince themselves a correct solution is broken.
- **Forgetting `heapq` is min-only.** Half of all heap bugs. Negate, and negate
  *back*, and don't forget the second one.
- **Negating the whole tuple** when you only meant the key. `(-count, -word)`
  doesn't compile for strings; `(-count, word)` is what you wanted — and note it
  flips ties to *ascending* word order, which may or may not be the spec.
- **Using a max-heap for "k largest".** It's backwards, it's O(n log n) space
  O(n), and it can't run on a stream. You want a **min**-heap of size k.
- **Claiming heapify is O(n log n).** It's O(n). Say so, and say why (the leaves
  cost nothing). Conversely, don't claim heapsort beats sorting — it doesn't.
- **Reaching for a heap when you need search.** Membership and arbitrary
  deletion are O(n). If the problem says "remove this specific task", a bare
  heap is the wrong structure.
- **Reaching for a heap when one sort would do.** If you need the order once and
  never touch the data again, `sorted()` is shorter, faster to write, and easier
  to defend. A heap that's built and drained exactly once bought you nothing.
- **Comparison crashes on tie.** Tuples compare elementwise; the moment two
  priorities tie, Python compares the payloads. Insert a monotonic counter.
- **Off-by-one in the index arithmetic** if you hand-roll one: children are
  `2i+1` and `2i+2` for **0-indexed** arrays. The `2i` / `2i+1` form you'll find
  in older textbooks is for 1-indexed arrays. Pick one and stay in it.

---

## 8. Worked example

Not from the problem set, so nothing is spoiled.

> **Problem.** (Last Stone Weight.) You have stones with integer weights. Each
> turn, smash the two **heaviest** together: if they're equal, both are
> destroyed; otherwise the lighter is destroyed and the heavier is replaced by
> the difference. Repeat until at most one stone is left; return its weight, or
> 0.
> `[2,7,4,1,8,1]` → `1`.

**Brute force.** Each turn, scan the list twice to find the two largest, remove
them, append the difference. Each turn is O(n) and there are up to n turns:
**O(n²)**. Say this out loud first — always.

**Spot the pattern.** The scan exists only to find the maximum. And the
collection *changes every turn* — one or two removed, sometimes one added. That
is Pattern A's trigger exactly: **repeated access to the extreme, over a
changing collection.** Sorting doesn't help, because the collection changes
after every extraction and you'd re-sort each time.

```python
import heapq

def last_stone_weight(stones):
    heap = [-s for s in stones]        # negate: heapq is a MIN-heap
    heapq.heapify(heap)                # O(n), not O(n log n)

    while len(heap) > 1:
        first  = -heapq.heappop(heap)  # heaviest    — un-negate
        second = -heapq.heappop(heap)  # second heaviest
        if first != second:
            heapq.heappush(heap, -(first - second))

    return -heap[0] if heap else 0     # the empty case is a real input
```

**Complexity.** Building the heap is O(n). Each turn destroys at least one stone,
so there are at most n turns, each doing O(log n) work: **O(n log n) time, O(n)
space.** From O(n²) to O(n log n) by changing nothing but the container.

**Edge cases to state before declaring done:** one stone (`[1]` → `1`), two
equal stones (`[2,2]` → `0`, and note the heap is then empty — that `if heap`
guard is not decoration), all equal weights.

**The move to internalise:** the brute force had a scan whose only job was
*finding the extreme*, inside a loop that *mutated the collection*. That
combination is the heap's signature. In Arrays & Hashing you learned to ask "is
this inner loop just searching?" — here the question is **"is this inner loop
just finding the max, over and over?"**

---

## 9. Materials

Full list on [[../meta/resources|Resources]]. For this lesson, in order:

1. [Heap data structure](https://www.geeksforgeeks.org/heap-data-structure/) —
   the hub page. 10 min.
2. [Binary heap](https://www.geeksforgeeks.org/binary-heap/) — the array
   representation and the sift operations, with code. This is the one that
   matters; read the insert and extract sections slowly.
3. [`heapq` — Python docs](https://docs.python.org/3/library/heapq.html) — short,
   and the "Theory" note at the bottom is genuinely good. Know
   `heappushpop` vs `heapreplace` from here.
4. [Python complexity cheat sheet](https://www.geeksforgeeks.org/complexity-cheat-sheet-for-python-operations/) —
   already open from Lesson 1. Check the heap rows.
5. [NeetCode on YouTube](https://www.youtube.com/@NeetCode) — search the problem
   name, but **only after** a timed attempt.

If you want one more rep on the k-largest idiom before the timed box, search
LeetCode for "Kth Largest Element in a Stream" — it's Easy, it is not in the
Blind 75, and it is Pattern B with the padding removed.

---

## 10. Self-check

Aloud, in full sentences, before starting the timer.

1. State the heap property. Does it say anything about siblings?
2. Given index `i` in a 0-indexed heap array, where are its children and its
   parent?
3. Why can a heap be stored in a flat array with no pointers — which of the two
   heap rules makes that possible?
4. Walk through `push` and `pop` in words. Which child do you swap with on the
   way down, and why that one?
5. Why is `peek` O(1) but `pop` O(log n)?
6. Why is `heapify` O(n) and not O(n log n)? Give the leaves argument.
7. How do you get a max-heap out of `heapq`? What's the second half of that
   trick that people forget?
8. To keep the **k largest** elements, do you use a min-heap or a max-heap? Say
   why in one sentence, and give the complexity.
9. When is sorting the better tool than a heap? Give the rule in one line.
10. What is a heap *bad* at? Name two operations and their cost.
11. Give the trigger phrase for each of patterns A–D.

If 6 or 8 come out mumbled, go back to section 3 and section 5. Those two are
the ones that get asked.

---

## 11. Ready?

**First timed problem: Find Median from Data Stream** (Hard) — Pattern C.

Say `timed find-median-from-data-stream` and I'll give you the statement and
start the 30-minute box. No hints while it's running.

This is a **Hard**, and the first one in the curriculum. Not finishing is the
expected outcome, not a failure — it requeues at +2 days and comes back until
it's cold-solvable. What matters is that the 30 minutes produce evidence about
where the ceiling actually is.

**What "good" looks like here** — even if you don't finish:

- You notice within the first five minutes that a **sorted list** solves it with
  O(n) insert, state that as the baseline, and state its complexity. Getting to
  a working-but-slow answer first is a win, not a detour.
- You articulate *why* one heap isn't enough — the median is a boundary, not an
  extreme — before reaching for two.
- You write the **invariant** down explicitly (which half is which, how sizes
  relate) before writing the rebalance code. Every bug in this problem is an
  invariant bug.
- You remember the negation for the max-heap half, *both* directions.
- You handle the even/odd split and the very first insertion into an empty
  structure.
- You state complexity per operation separately: `addNum` and `findMedian` are
  not the same cost, and saying so unprompted is the mark of someone who
  understands the structure.

After the box, say `debrief` and show the code — finished or not, working or
not. The unfinished attempts are the highest-signal thing in the vault.
