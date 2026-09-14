---
type: concept
status: unseen
updated: 2026-09-14
sources: [https://www.geeksforgeeks.org/greedy-algorithms/]
tags: [greedy, arrays]
---

# Reachability Frontier

**In one sentence, in my own words** — instead of enumerating paths, carry a
single number: the furthest index anything seen so far can reach. Sweep left to
right widening it, and fail the instant the index you're standing on has fallen
behind it.

Pattern B in [[../lessons/15-greedy|Lesson 15 — Greedy]].

## When to reach for it

Trigger: **"can you reach the end"**, "is it possible to get to X", "minimum
jumps", where each position grants you a **range** of next positions rather than
one specific successor.

The deeper trigger is a reduction worth internalising:

> The problem asks **whether** X is reachable, not **which path** reaches it.

Any time that's true, look for a single boundary you can carry instead of paths
you'd have to enumerate. The path information is not part of the answer, so
don't pay to compute it.

Anti-trigger: if the problem wants the actual route reconstructed, the frontier
has thrown away exactly what you need. That's a [[bfs-graph|BFS]] or a DP.

## How it works

The brute force instinct is a search: from each index, branch to every index it
can reach, recurse. Exponential; memoising gives an O(n²) DP.

The greedy collapse: **you don't care which path got you somewhere, only how far
anything can get.** So:

```
reach = 0
for each index i, left to right:
    if i > reach:  nothing further is reachable — stop, answer is no
    reach = max(reach, i + nums[i])
```

Two claims carry it, and both are worth saying aloud:

1. **The frontier is monotone** — it only ever moves right, because you only
   ever `max` into it. So no later choice can want an earlier one back, which is
   precisely why a greedy commit is safe here (see [[greedy-choice]]).
2. **Everything behind the frontier is reachable**, not just the endpoint. Steps
   grant a *range*, so if you can land on index `k` you can land on anything
   between here and `k`. That's what makes one number sufficient to summarise an
   entire set of reachable positions.

The whole problem reduces to one comparison per index: *am I still inside the
frontier?*

## Minimal example

```
index:     0    1    2    3    4
values:  [ 2,   3,   1,   1,   4 ]      (each = how far you may step)

i=0   0 <= reach(0) ok   reach = max(0, 0+2) = 2     ▓▓▓░░░
i=1   1 <= reach(2) ok   reach = max(2, 1+3) = 4     ▓▓▓▓▓▓▓▓▓
i=2   2 <= reach(4) ok   reach = max(4, 2+1) = 4
i=3   3 <= reach(4) ok   reach = max(4, 3+1) = 4
i=4   4 <= reach(4) ok   → the end is inside the frontier: yes
```

And a failure, `[3, 2, 1, 0, 4]`:

```
i=0  reach = 3
i=1  reach = max(3, 3) = 3
i=2  reach = max(3, 3) = 3
i=3  reach = max(3, 3) = 3     ← value 0, frontier stuck
i=4  4 > reach(3)              ← STOP. index passed the frontier.
```

```python
reach = 0
for i, step in enumerate(nums):
    if i > reach:
        return False
    reach = max(reach, i + step)
return True
```

Three lines of state. The `if` before the `max` is not stylistic — checking
after would let an unreachable index widen the frontier with a step it was never
allowed to take.

## Complexity

**O(n) time, O(1) space.** One pass, one int.

Compare: the memoised search is O(n²) time and O(n) space, because it asks "can
I reach the end *from index i*" for every `i` and each of those scans a range.
The frontier answers the question once for the whole array instead of n times,
which is where the factor of n goes.

## Gotchas

- **Treating the frontier as "where I am".** It is the furthest point reachable
  from *anything seen so far*. It is usually far ahead of the current index and
  it never moves backwards.
- **Checking `i > reach` after widening instead of before.** Lets a position you
  can't stand on contribute its step.
- **Comparing against `len(nums)` instead of `len(nums) - 1`.** The last valid
  index is `n-1`. Classic off-by-one, and the loop-exit version of it is worse
  because it silently returns the wrong boolean.
- **Breaking early on a zero.** A `0` is only fatal if the frontier doesn't
  already extend past it. `[3, 0, 0, 0]` succeeds.
- Assuming it generalises to "minimum number of jumps" unchanged — that variant
  needs a second frontier (the end of the current jump's range) alongside the
  first. Same idea, two variables.

## Related

- [[greedy-choice]] — the property; here it holds because the frontier is
  monotone, which is about as clean as the argument ever gets
- [[kadane]] — the other one-variable greedy scan in Lesson 15; carries a running
  sum that can reset, where the frontier never resets
- [[bfs-graph]] — what you'd need if the question were *which* path, not
  *whether*; the frontier is the cheap answer available only because the path is
  not part of the output
- [[loop-invariant-bounds]] — the frontier is exactly an invariant maintained
  across the loop, and stating it that way is how you justify the code

## Evidence

Problems where I used this unaided:
