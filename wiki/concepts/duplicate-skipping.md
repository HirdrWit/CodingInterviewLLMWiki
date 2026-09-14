---
type: concept
status: unseen
updated: 2026-09-14
sources: []
tags: [two-pointers, algorithms]
---

# Duplicate Skipping

**In one sentence, in my own words** — on sorted input, equal values sit next to
each other, so advancing past any value identical to the one just used stops an
enumeration from emitting the same answer twice — without a set, and without
extra space.

Introduced in Pattern C of [[../lessons/02-two-pointers|Lesson 2]]. It is a
**correctness** technique, not an optimisation, and that distinction matters:
forgetting it produces wrong output, which reads far worse in an interview than
a slow-but-right solution.

## When to reach for it

Trigger: the problem says **"return all unique …"**, **"no duplicate triplets"**,
**"each combination once"** — and you are *enumerating* answers rather than
counting or returning a single one.

Also: any time you're about to deduplicate a result list at the end with a set.
That's the tell. If the enumeration can avoid generating the duplicate in the
first place, it should — the set costs O(n) space and, for lists of values, needs
them tupled to be hashable at all.

## How it works

Sorting first (see [[sort-then-scan]]) makes equal elements adjacent. Then
"have I used this value already?" becomes a comparison with the neighbour — a
local, O(1), zero-space check.

```python
nums.sort()
for i in range(len(nums)):
    if i > 0 and nums[i] == nums[i - 1]:
        continue          # this value was already the anchor; skip it
    ...
```

`i > 0 and nums[i] == nums[i-1]` — compare **backwards**, not forwards. Looking
back skips the *second and later* copies and keeps the first, which is what you
want. Looking forward (`nums[i] == nums[i+1]`) skips the first and keeps the
last, which is subtly different and breaks when the run reaches the end of the
array.

### The two places it is needed

In the fix-one-then-converge shape, duplicates leak from **two** independent
sources, and handling only one of them still leaks:

1. **The anchor.** If the fixed element repeats, the identical inner search runs
   again and re-emits the same answers. Skip repeated anchors.
2. **The pointers, after a successful hit.** Once a valid combination is
   recorded, both pointers must move *past* their entire run of equal values —
   otherwise the very next iteration finds the same combination with a different
   pair of indices.

```python
# after recording a hit, inside the converging loop:
while lo < hi and nums[lo] == nums[lo + 1]:
    lo += 1
while lo < hi and nums[hi] == nums[hi - 1]:
    hi -= 1
lo += 1        # and then the normal single step past the last copy
hi -= 1
```

Note the `lo < hi` guard on both inner loops, and note that the skip loops leave
the pointers *on* the last copy — the final `+= 1` / `-= 1` is still required.
Off-by-one here is the usual bug.

## Complexity

Free. The skip loops only advance pointers the outer loop was going to advance
anyway, so total movement is still bounded by n — the technique adds **O(1)
space and no asymptotic time**.

That is the entire argument for doing it this way rather than collecting
everything and deduplicating at the end, which costs O(k) space in the number of
answers plus the hashing.

## Gotchas

*This section grows every time I get it wrong. That's its job.*

- **Handling only one of the two places.** Anchor skipping alone, or pointer
  skipping alone, both still leak duplicates. It has to be both.
- **Comparing forwards instead of backwards** on the anchor. Off-by-one at the
  end of the array.
- **Missing the `i > 0` guard** → `nums[-1]`, which in Python silently reads the
  last element instead of raising. Wrong answer, no error, hardest kind to spot.
- **Missing the `lo < hi` guard** in the inner skip loops → the pointers cross
  and index out of range.
- **Forgetting the final single step** after a skip loop. The loop stops *on*
  the last duplicate, not past it — infinite loop if you don't advance.
- Applying it to unsorted input. Equal elements aren't adjacent, so the neighbour
  check finds nothing and every duplicate survives.
- Using it when duplicates are actually *wanted* — "count the pairs" is not
  "list the unique pairs". Read the statement.

## Related

- [[sort-then-scan]] — the precondition. This technique is only cheap because
  sorting made equal values neighbours.
- [[opposite-end-pointers]] — the loop this bolts onto.
- [[seen-set]] — the alternative: dedupe with a set at the end. O(n) space, needs
  hashable elements, and hides the fact that the enumeration was generating
  garbage. Prefer skipping.
- [[../skills/algorithms|Algorithms]] — parent skill.

## Evidence

Problems where I used this unaided:

*None yet. Add links here — and only then promote the status.*
