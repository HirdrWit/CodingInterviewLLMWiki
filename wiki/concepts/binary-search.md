---
type: concept
status: unseen
updated: 2026-09-14
sources: [https://www.geeksforgeeks.org/binary-search/, https://docs.python.org/3/library/bisect.html]
tags: [binary-search]
---

# Binary Search

**In one sentence, in my own words** — repeatedly throw away the half of the
search space that I can *prove* contains no answer, which costs O(log n) probes
instead of an O(n) scan.

Pattern A in [[../lessons/05-binary-search|Lesson 5]]. The first tool in the
curriculum that does not touch every element.

## When to reach for it

Triggers: "sorted array", "O(log n) required", "n up to 10^9" (an O(n) scan is
dead at that size), "find the first/last position", "minimum k such that…".

The deeper trigger, which is the one that transfers: **the brute force is
scanning candidates in order, looking for where an answer flips from no to
yes.** That scan is what binary search replaces — whether the candidates are
array indices or a range of possible answers.

Not for pure membership. A [[hash-map]] answers "is x in here" in O(1) average.
Binary search earns its place on *order* questions: nearest, first ≥, boundary,
minimum.

## How it works

One template, memorised, written without thinking:

```python
lo, hi = 0, len(nums) - 1        # invariant: the answer is inside [lo, hi]
while lo < hi:
    mid = lo + (hi - lo) // 2    # floors — leans toward lo
    if condition(mid):
        hi = mid                 # mid might BE the answer — keep it
    else:
        lo = mid + 1             # mid ruled out — discard it
return lo                        # lo == hi: one candidate left
```

The two branches must between them shrink the window every iteration. Because
`mid` floors toward `lo`, the `lo` branch is the one that must exclude `mid`.
See [[loop-invariant-bounds]] for the `<` vs `<=` fork and the ceiling-midpoint
variant.

`mid = lo + (hi - lo) // 2` rather than `(lo + hi) // 2`: the latter overflows in
fixed-width integer languages. Python can't overflow, but write it the safe way
anyway — it costs nothing and interviewers know the history.

## Minimal example

```python
import bisect

a = [1, 3, 3, 5, 8]
bisect.bisect_left(a, 3)    # 1 — first index where a[i] >= 3
bisect.bisect_right(a, 3)   # 3 — first index where a[i] >  3
# count of 3s, in O(log n):
bisect.bisect_right(a, 3) - bisect.bisect_left(a, 3)   # 2
```

`bisect` is the right answer in production. Say so, then hand-write it anyway,
because that's what's being tested.

## Complexity

**O(log n) time, O(1) space** iterative. Each step halves the candidates:
n / 2^k = 1 gives k = log₂ n. A million elements is 20 probes.

Recursive is the same time but **O(log n) space** for the call stack — prefer
iterative and say why.

Requires **O(1) random access**. On a linked list, finding the middle is itself
O(n), so you'd pay O(n) per probe — the algorithm doesn't apply usefully. This
is a common follow-up question.

If the predicate check is itself O(n) (searching an answer range), the total is
O(n log R). State both factors.

## Gotchas

- `lo = mid` with a floor midpoint is an infinite loop at the two-element
  window. Floor-mid pairs with `lo = mid + 1`.
- Returning `mid` early answers "find a value", not "find the first one". With
  duplicates that's the wrong answer — converge instead.
- Sorting an unsorted input just to binary search once costs O(n log n) and
  saves less. Only pays for repeated searches.
- Claiming O(log n) when the inner feasibility check is O(n).

## Related

- [[loop-invariant-bounds]] — the off-by-one discipline; that page is where the
  bugs actually live, this page is only the idea
- [[search-space-invariant]] — the generalisation: what you may binary search
  over, and why sortedness is only a special case
- [[hash-map]] — beats this on membership, loses on every order question
- [[two-pointers]] — the other way to exploit sortedness, in O(n) but O(1) space

## Evidence

Problems where I used this unaided:

*None yet — topic not started.*
