---
type: concept
status: learning
updated: 2026-09-10
sources: []
tags: [arrays-hashing]
---

# Frequency Map

**In one sentence:** Count occurrences into `value → count`, so questions about
*how many* become O(1) lookups instead of repeated scans.

## When to reach for it

Trigger words: **"anagram"**, "how many times", "most common", "appears more
than k times", "same characters", "duplicate counts".

The deeper tell: the problem cares about the *multiset* of elements — what's
present and how often — but not their order.

## How it works

One pass builds the map. Two collections are anagrams exactly when their
frequency maps are equal, which is why the comparison is O(n) rather than
O(n log n) sorting.

```python
from collections import Counter
counts = Counter(nums)          # one pass, O(n)
counts["a"]                     # O(1), returns 0 for missing keys
```

## Complexity

O(n) time to build, O(k) space where k is the number of distinct values.
`Counter.most_common()` **sorts** — that's O(n log n), so don't reach for it if
you claimed O(n). Bucket sort by count gets you back to O(n).

## Gotchas

- `most_common()` is not free
- Comparing `Counter` objects directly works and is clean: `Counter(a) == Counter(b)`
- If the alphabet is fixed and small (26 lowercase letters), a plain array of
  size 26 beats a dict — mention it, interviewers like it

## Related

- [[hash-map]] — the underlying structure
- [[canonical-key]] — grouping by an equivalence rather than counting

## Evidence

*None yet.*
