---
type: concept
status: unseen
updated: 2026-09-14
sources: [https://www.geeksforgeeks.org/overlapping-subproblems-property-in-dynamic-programming-dp-1/, https://www.geeksforgeeks.org/optimal-substructure-property-in-dynamic-programming-dp-2/]
tags: [algorithms, 1d-dynamic-programming]
---

# Dynamic Programming

**In one sentence, in my own words** — recursion over a tree of choices where the
same subproblem keeps reappearing, plus a memory so each one is only ever
computed once; the memory is what collapses exponential work into linear.

Taught in [[../lessons/13-1d-dynamic-programming|Lesson 13]]. Its blunt summary:
**DP is [[../curriculum/backtracking|backtracking]] that keeps its notes.**

## When to reach for it

Four signals. Two or more together and run the four questions:

- **"How many ways…"** — counting over a tree of choices.
- **"Minimum / maximum …"** where each step is a *choice*, not a scan.
- **"Can I reach / is it possible to …"** — a boolean over reachable states.
- **The brute force re-explores the same subproblem.** The decisive one. Sketch
  the recursion tree; if the same argument shows up at two different nodes,
  it's DP.

Anti-signals: a provably-safe local rule means greedy, not DP. A contiguous
window with a monotone rule usually means sliding window.

## The two properties that license it

- **Overlapping subproblems** — the same subquestion is asked more than once. No
  overlap, no point memoising. Merge sort has subproblems but never the *same*
  one twice, which is why it's divide-and-conquer rather than DP. Worth being
  able to say.
- **Optimal substructure** — the best answer to the big problem is built from
  best answers to smaller ones.

## How it works

The four questions, in order. They are the method; the code is the leftovers.

```
1. STATE      — what does dp[i] MEAN, in one precise English sentence?
2. RECURRENCE — how is dp[i] built from smaller answers? what are the choices?
3. BASE CASES — the smallest inputs answerable with no help, read off the
                problem statement (not guessed from the recurrence).
4. ORDER      — which fill direction has every dependency ready when needed?
```

Then three mechanical levels: naive recursion → add
[[memoization|a memo]] → flip to [[bottom-up-tabulation|a table]] → optionally
drop to [[rolling-variables|rolling variables]].

## Minimal example

```python
def fib(n):                       # naive: O(2^n), recomputes fib(3) many times
    if n < 2:
        return n
    return fib(n-1) + fib(n-2)
```

Two distinct facts per level: n states, O(1) work each, so the memoized version
is O(n) and the naive one is O(2ⁿ) purely because it forgets.

## Complexity

**Time = number of states × work done at each state.** The one formula.

- 1-D state, O(1) transition → O(n)
- 1-D state, inner loop over earlier states → O(n²)
- State indexed by a target *value* → O(target × items), which is
  **pseudo-polynomial**: it scales with the numeric value of the input, not its
  length. Say so before being asked.

Space is O(states) for the table, plus O(depth) call stack if top-down.

## Gotchas

- Writing code before the state is defined in English. The expensive one.
- "Best in the first i" vs "best ending at i" — different states, different
  recurrences, and the answer is `dp[-1]` for one and `max(dp)` for the other.
- Base cases invented to make the recurrence look tidy, rather than derived from
  the problem statement.
- Claiming polynomial time on a value-indexed DP.
- Assuming DP applies without checking overlap — if every subproblem is
  distinct, the memo is pure overhead.

## Related

- [[memoization]] — DP top-down: the recurrence plus a cache
- [[bottom-up-tabulation]] — DP bottom-up: the same recurrence as a loop
- [[state-transition]] — the four questions in detail; the part that transfers
- [[rolling-variables]] — the space optimisation that follows from the recurrence's reach

## Evidence

Problems where I used this unaided:

*None yet.*
