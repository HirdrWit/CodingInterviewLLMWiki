---
type: concept
status: learning
updated: 2026-09-10
sources: []
tags: [arrays-hashing]
---

# Complement Lookup

**In one sentence:** Instead of searching for a *pair* that sums to a target,
search for the one exact *partner* each element needs — which a hash map can
answer in O(1).

## When to reach for it

Trigger: "find two numbers that sum to `target`", "a pair such that…".
Generally: any time the nested loop is checking a condition that can be
**rearranged to solve for the second element**.

## How it works

At value `x` with target `t`, the needed partner is exactly `t - x` — one
specific value, not a range. So ask a map "have I already passed `t - x`?"

**Build the map as you scan, not before.** That ordering is what prevents an
element from pairing with itself, and it's the detail most people get wrong.

```python
seen = {}                          # value -> index
for i, x in enumerate(nums):
    if target - x in seen:
        return [seen[target - x], i]
    seen[x] = i                    # AFTER the check
```

## Complexity

O(n) time, O(n) space — one pass, one map.

## Gotchas

- Inserting before checking → an element matches itself
- Duplicate values: storing `value → index` overwrites, which is usually fine
  for "return any one pair" but not for "return all pairs"
- If the array is **sorted**, [[two-pointers]] does this in O(1) space — know
  which fork you're on

## Related

- [[hash-map]] · [[two-pointers]]

## Evidence

*None yet.*
