---
type: concept
status: unseen
updated: 2026-09-14
sources: []
tags: [binary-search]
---

# Search-Space Invariant

**In one sentence, in my own words** — binary search is licensed not by
sortedness but by a **monotonic predicate**: a yes/no question over the search
space whose answer flips from false to true exactly once, never back.

Underpins Patterns B and C in [[../lessons/05-binary-search|Lesson 5]]. This is
the page that turns binary search from "a thing you do to sorted arrays" into a
general tool.

## When to reach for it

Whenever you're deciding *whether* binary search applies at all. Ask one
question:

> Is there a yes/no property of a candidate such that, once it becomes true, it
> stays true for every larger candidate?

If yes, halve. If you can't say in one sentence *why* `P(x) ⟹ P(x+1)`, you do
not have a binary search — you have a guess, and the interviewer will ask you to
justify it.

Triggers that mean the invariant is hiding somewhere other than a sorted array:

- "minimise the maximum", "maximise the minimum", "the smallest capacity /
  speed / size that works" → the space is the **range of possible answers**
- "rotated sorted array", "sorted but shifted" → the sort invariant is **broken
  but not destroyed**
- "a function you can only sample", "guess the number" → the space is abstract

## How it works

Draw the space and mark the predicate:

```
space:      0    1    2    3    4    5    6    7    8
P(i):       F    F    F    F    T    T    T    T    T
                                ↑ the boundary — this is what you find
```

Binary search finds a **boundary**, not an element. Probe the middle: if `P(mid)`
is true, the boundary is at `mid` or left of it; if false, it's strictly right.
Either way half the space dies.

If the pattern were `F T F T T`, probing the middle tells you nothing and no
cleverness recovers it. **Monotonicity is the whole licence.**

A sorted array is the special case where the space is indices and the predicate
`nums[i] >= target` happens to be monotonic *because* it's sorted.

**The broken-invariant case.** A rotated sorted array is two sorted runs:

```
[ 4  5  6  7 | 0  1  2 ]
  └── run A ─┘ └─run B┘      every A > every B
```

The obvious predicate fails. The one that survives is weaker but sufficient:
**at least one side of any midpoint is a clean sorted run.** Compare `nums[mid]`
to a fixed endpoint to find which side is clean; reason normally on that side.
The general habit — *when the obvious invariant breaks, hunt for the weaker one
that survives* — is worth more than the rotated-array trick itself.

## Minimal example

Binary search on the answer, with an unsorted input:

```python
def smallest_feasible(lo, hi, feasible):
    # precondition: feasible is monotonic, and feasible(hi) is True
    while lo < hi:
        mid = lo + (hi - lo) // 2
        if feasible(mid):
            hi = mid
        else:
            lo = mid + 1
    return lo
```

Nothing here mentions an array. The input appears only inside `feasible`.

## Complexity

O(log R) probes, where **R is the size of the search space** — which is the
answer *range*, not the input length, when you're searching answers. If each
`feasible` check scans the input, the total is **O(n log R)**. Say both factors;
claiming O(log R) alone is a common and costly overstatement.

Space O(1).

## Gotchas

- **Asserting monotonicity without checking it.** The most expensive mistake
  here. "Eating faster never takes longer" is a proof; "it feels monotonic" is
  not.
- **Bounds that exclude the answer.** Pick `lo` and `hi` so the answer is
  provably inside, and justify both. If `feasible(hi)` is false, `while lo < hi`
  returns garbage rather than failing loudly.
- **Duplicates destroying the rotated-array reasoning.** With
  `nums[lo] == nums[mid] == nums[hi]` you can't tell which side is clean, and
  worst case degrades to O(n). If the constraints promise distinct values, say
  out loud that you're relying on it.
- **Mixing anchors.** Comparing `nums[mid]` to `nums[lo]` and to `nums[hi]` give
  two different, both-valid case analyses. Switching mid-solution produces code
  that passes half the tests.

## Related

- [[binary-search]] — the mechanism this page justifies
- [[loop-invariant-bounds]] — once you know the space is searchable, this is how
  you avoid the off-by-ones while searching it
- [[two-pointers]] — the other "exploit structure to avoid a nested loop" tool;
  it needs actual sortedness, where this needs only monotonicity

## Evidence

Problems where I used this unaided:

*None yet — topic not started.*
