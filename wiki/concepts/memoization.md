---
type: concept
status: unseen
updated: 2026-09-14
sources: [https://www.geeksforgeeks.org/tabulation-vs-memoization/, https://docs.python.org/3/library/functools.html]
tags: [algorithms, 1d-dynamic-programming, 2d-dynamic-programming]
---

# Memoization

**In one sentence, in my own words** — write the naive recursion, then hang a
cache on it: check the cache on the way in, fill it on the way out, change
nothing else — and the recursion tree collapses into a path.

Level 2 of the three levels in [[../lessons/13-1d-dynamic-programming|Lesson 13]].
The top-down half of [[dynamic-programming]].

## When to reach for it

Trigger: **you already have a correct recursion and it is too slow.** That is
the whole trigger. You do not decide to memoise in the abstract — you write the
brute force, sketch the tree, see the same argument at two different nodes, and
add the cache.

In the 30-minute box it is also the *default first implementation* of any DP.
It is the recurrence plus two lines, so it is the fastest correct thing you can
produce, and a correct memo beats a half-written table.

## How it works

```python
def fib(n, memo={}):
    if n < 2:                              # base case, unchanged
        return n
    if n in memo:                          # 1. check the whiteboard
        return memo[n]
    memo[n] = fib(n-1, memo) + fib(n-2, memo)   # 2. compute
    return memo[n]                         # 3. write it up
```

Three edits to the naive version, none of which touch the logic. The recurrence
is still the recurrence; the cache is pure bookkeeping bolted on the outside.

In Python, `@cache` (3.9+) or `@lru_cache(maxsize=None)` does exactly this for
any pure function. Legitimate in an interview — but say *"this memoises on the
arguments"* rather than letting it read as magic, and be ready to write the
dict by hand when asked, because some interviewers will ask precisely that.

## Why it collapses exponential to polynomial

The sentence to be able to say cold:

> There are **n distinct states**, each is computed **exactly once**, and each
> costs **O(1) work** on top of its recursive calls — so O(n).

The naive version is O(2ⁿ) *purely because it forgets*. Nothing about the
arithmetic changed. The exponent came from re-deriving the same answers, and
the cache is just a place to write each answer down once. Two facts to separate
and state separately: **how many states there are**, and **what each one costs**.

## Complexity

**Time = number of states × work per state.** Space = O(states) for the cache,
**plus O(depth) for the call stack** — the stack term is the one people forget,
and it is the whole reason [[bottom-up-tabulation]] exists.

## Gotchas

- **The recursion limit.** CPython stops at 1000 frames by default. A top-down
  solution on a 10⁵-length input raises `RecursionError` no matter how good the
  memo is. This is the practical reason to know the bottom-up flip.
- **Mutable default argument.** `memo={}` is created once and shared across
  every call to the function — fine while solving one input, a real bug if the
  function is called again with different data. Pass the dict explicitly, use an
  inner closure, or use `@cache`.
- **Unhashable arguments.** `@lru_cache` keys on the arguments, so pass
  **indices, never slices or lists**. Slicing inside the recursion is also O(k)
  per call, which quietly destroys the complexity you just claimed.
- **Caching a function that isn't pure.** If the result depends on anything but
  the arguments, the cache returns a stale answer and nothing looks wrong.
- **Memoising where there is no overlap.** No repeated subproblem, no speedup —
  the cache is pure overhead. Check the tree first.

## Related

- [[dynamic-programming]] — the parent idea; this is its top-down half
- [[bottom-up-tabulation]] — the same recurrence pushed forward instead of
  pulled backward: no stack and easy space optimisation, but it computes every
  state whether or not anything reaches it
- [[state-transition]] — what you must have written down *before* the cache is
  worth adding; the memo speeds up a recurrence, it does not find one
- [[dfs-traversal]] — the traversal underneath; memoization is DFS over a DAG of
  subproblems that refuses to revisit a node, the same instinct as a
  [[visited-set]] but storing the *answer* rather than just the fact of the visit

## Evidence

Problems where I used this unaided:

*None yet.*
