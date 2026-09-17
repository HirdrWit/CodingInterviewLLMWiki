---
type: concept
status: solid
updated: 2026-09-16
sources: [wiki/problems/top-k-frequent-elements.md]
tags: [arrays-hashing, pattern]
---

# Bucket Sort (by frequency)

**The tell:** you need the *top* or *bottom* `k` by count, and sorting is too
slow. Frequencies are bounded by `n`, so they can be used as **array indices**
instead of being compared.

Comparison sorting costs O(n log n) because it only ever asks "is a > b". If the
thing being ordered is a small integer, that question is unnecessary — put it at
that index. That is the whole trick, and it is why bucket sort beats the
comparison lower bound: **it isn't a comparison sort.**

## Shape

```python
count = Counter(nums)
buckets = [[] for _ in range(len(nums) + 1)]
for num, freq in count.items():
    buckets[freq].append(num)

result = []
for freq in range(len(buckets) - 1, 0, -1):
    for num in buckets[freq]:
        result.append(num)
        if len(result) == k:
            return result
```

`buckets[f]` holds the numbers occurring exactly `f` times. `n + 1` slots,
because a frequency can be `n`.

**O(n) time, O(n) space.**

## The cumulative variant

A different formulation, reached cold on
[[../problems/top-k-frequent-elements|Top K Frequent Elements]] 2026-09-16:
append on every increment and never remove, so `bucket[i]` holds every element
occurring **at least** `i+1` times. The lists then grow monotonically as `i`
falls, and the first one of length exactly `k` is the answer.

Same O(n), and it collects the answer in one comparison rather than a loop. **But
it depends on the top-k being unambiguous** — with a tie at the boundary the
length skips past `k` and is never equal to it. The standard form above degrades
gracefully there; this one returns nothing.

Worth knowing as a lesson in its own right: **a solution can be correct only
because of a constraint, and that dependency should be said out loud.**

## When it is the wrong tool

- **The range of counts is unbounded or huge** relative to `n` — the bucket array
  is then mostly empty and the space is wasted.
- **A full ordering is needed**, not just the top `k`.
- **Streaming input**, where `n` is not known up front. A heap of size `k` is the
  tool there: O(n log k), worse asymptotically but bounded in memory.

## Versus a heap

The obvious alternative for top-k is a min-heap of size `k`, O(n log k). Bucket
sort is better here — O(n) — because the key insight applies: frequencies are
bounded by `n`. Naming the heap and rejecting it with that reason is the strong
interview answer.

## Evidence

- [[../problems/top-k-frequent-elements|Top K Frequent Elements]] — solved cold
  unaided 2026-09-16, 18 min 41 s, O(n), stated follow-up bound cleared.

## Related

[[frequency-map|Frequency Map]] · [[constraint-bounded-complexity|Constraint-Bounded Complexity]] ·
[[hash-map|Hash Map]]
