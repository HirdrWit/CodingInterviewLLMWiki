---
type: concept
status: unseen
updated: 2026-09-10
sources: []
tags: [algorithms, example]
---

# Two Pointers

> **This page is a worked example of the concept shape**, seeded when the wiki
> was created. Its status is `unseen` because no evidence backs it yet — it's
> here to show what a real concept page looks like, not to claim knowledge.
> Overwrite it the first time you actually learn this.

**In one sentence:** Walk two indices through a sequence under a rule that lets
each one only move forward, so a pair-search that looks quadratic finishes in one
pass.

## When to reach for it

The trigger, not the mechanics — this is the half that transfers:

- The input is **sorted**, or sorting it doesn't break the problem
- You're looking for a **pair** (or triple) that satisfies some condition
- A brute-force answer is "check every pair", i.e. O(n²)
- The condition is **monotonic**: moving a pointer one way always pushes the
  value one way

If the array isn't sorted and can't be, this usually isn't the tool — reach for
a hash map instead.

## How it works

Put one pointer at each end. Compare the current pair against the target.
Because the array is sorted, a too-small sum can only be fixed by moving the
left pointer right; a too-large sum, by moving the right pointer left. Each move
permanently discards a set of pairs that can't work. The pointers meet after at
most n moves.

## Minimal example

```python
def two_sum_sorted(nums, target):
    lo, hi = 0, len(nums) - 1
    while lo < hi:
        s = nums[lo] + nums[hi]
        if s == target:
            return (lo, hi)
        if s < target:
            lo += 1      # only a bigger left value can help
        else:
            hi -= 1      # only a smaller right value can help
    return None
```

## Complexity

O(n) time — each pointer moves at most n steps and never backtracks.
O(1) space. If sorting is needed first, the sort dominates at O(n log n).

## Gotchas

*This section grows every time I get it wrong. That's its job.*

- `lo < hi` vs `lo <= hi` — off by one, and whether an element may pair with itself
- Duplicates: skipping them is usually required in the 3-sum family, and easy to forget
- Assuming sorted input when the problem doesn't guarantee it

## Related

- [[../skills/algorithms|Algorithms]] — parent skill
- Sliding window — also two indices, but both move *forward* and the interest is
  the range between them, not the pair at the ends. Different trigger: subarrays,
  not pairs.
- Binary search — the other "sorted input" tool. Reach for it when searching for
  one value, not a pair.

## Evidence

Problems solved unaided using this:

*None yet. Add links here — and only then promote the status.*
