---
type: concept
status: unseen
updated: 2026-09-14
sources: []
tags: [binary-search]
---

# Loop-Invariant Bounds

**In one sentence, in my own words** — before writing a binary search loop,
commit to a single sentence saying what `lo` and `hi` *mean*, and never write a
line that contradicts it; every off-by-one in this topic is that sentence
silently changing mid-function.

The discipline half of [[binary-search]]. The idea takes ten seconds to explain;
this page is why the implementation takes people years to get reliable.

## When to reach for it

Every single time you write a binary search. Not a pattern you recognise from a
problem statement — a habit you apply unconditionally.

The tell that you skipped it: you're adjusting `+1` and `-1` and re-running to
see what passes. That's not debugging, that's guessing, and an interviewer
watching you do it has learned everything they need to know.

## How it works

State the invariant first, as a comment, before the loop:

```python
# invariant: if an answer exists, it is at an index in [lo, hi]
lo, hi = 0, len(nums) - 1
```

Then every line must preserve it. `hi = mid` is legal only if `mid` could still
be the answer. `lo = mid + 1` is legal only if `mid` is definitively ruled out.

### The two templates

**Form 1 — converge on a boundary.** Use when an answer definitely exists and
you want to know *which* candidate it is: minimum element, first index ≥ target,
smallest feasible k.

```python
lo, hi = 0, len(nums) - 1
while lo < hi:                   # exits with ONE candidate: lo == hi
    mid = lo + (hi - lo) // 2
    if condition(mid):
        hi = mid                 # keep mid
    else:
        lo = mid + 1             # discard mid
return lo
```

**Form 2 — find an exact value or report absence.** Use when the target may
simply not be there.

```python
lo, hi = 0, len(nums) - 1
while lo <= hi:                  # exits with ZERO candidates: lo > hi
    mid = lo + (hi - lo) // 2
    if nums[mid] == target:
        return mid
    if nums[mid] < target:
        lo = mid + 1
    else:
        hi = mid - 1
return -1
```

Pick Form 1 as the default and reach for Form 2 deliberately. Two of them,
learned as units, beats one flexible one you tune per problem.

### Why `lo = mid` loops forever

```
window of 2:   lo         hi
               [ a    ,    b ]
mid = lo + (hi-lo)//2 = lo + 0 = lo      ← floor leans toward lo
```

`lo = mid` assigns `lo = lo`. Nothing shrinks. The loop spins.

**Rule: floor midpoint pairs with `lo = mid + 1`.** If the logic genuinely
requires `lo = mid`, switch to the ceiling midpoint
`mid = lo + (hi - lo + 1) // 2`, which pairs with `hi = mid - 1`. Never mix the
two.

### Inclusive vs exclusive `hi`

```python
lo, hi = 0, len(nums) - 1   # hi = "last index still possible"       (inclusive)
lo, hi = 0, len(nums)       # hi = "one past the last possible"      (exclusive)
```

These are different algorithms with different `-1`s throughout. Decide which
before the loop body exists, and say which out loud.

## Minimal example

The three hand-traces to run before declaring done. Thirty seconds, catches
almost everything:

```
n = 0   []          → does the loop body run at all? does the return index exist?
n = 1   [5]         → lo == hi immediately; Form 1 skips the loop entirely
n = 2   [5, 3]      → mid == lo; this is where infinite loops live
```

The two-element window is the home of every binary search bug. Trace it by
hand, not by running the code.

## Complexity

Doesn't change the O(log n) — this page is about correctness, not cost. But note
the one place bounds affect complexity: **converging when you should have
returned early** costs nothing asymptotically, while **walking left from a hit
to find the first occurrence** costs O(n) and destroys the whole point. Change
the predicate instead of walking.

## Gotchas

- Choosing `<` vs `<=` by feel. `while lo < hi` exits with one candidate;
  `while lo <= hi` exits with none. Different post-conditions, different returns.
- Writing `hi = mid - 1` in one branch and `hi = mid` in another in the same
  function. The invariant changed under you.
- Returning `mid` from inside the loop when the question is a boundary question
  — with duplicates, `mid` is an arbitrary member of the run, not the first.
- `(lo + hi) // 2` instead of `lo + (hi - lo) // 2`. Harmless in Python,
  a nine-year bug in the JDK, and a free point with an interviewer.
- Python's `//` floors toward negative infinity: `-3 // 2 == -2`. Bites on
  answer-space searches with negative bounds.

## Related

- [[binary-search]] — the algorithm these bounds implement
- [[search-space-invariant]] — the *other* invariant: that page says whether you
  may halve at all, this one says how to halve without an off-by-one
- [[two-pointers]] — also two indices converging, also needs a stated meaning
  for each; same discipline, easier bounds

## Evidence

Problems where I used this unaided:

*None yet — topic not started.*
