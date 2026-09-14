---
type: concept
status: unseen
updated: 2026-09-14
sources: [https://www.geeksforgeeks.org/tabulation-vs-memoization/]
tags: [algorithms, 1d-dynamic-programming, 2d-dynamic-programming]
---

# Bottom-Up Tabulation

**In one sentence, in my own words** — the same recurrence, run in the opposite
direction: start at the smallest inputs you can answer with no help, fill a table
forward until you reach the one that was asked, and never touch the call stack.

Level 3 of the three levels in [[../lessons/13-1d-dynamic-programming|Lesson 13]].
The bottom-up half of [[dynamic-programming]].

## When to reach for it

Trigger, in order of how often it actually decides things:

- **The input is large enough to blow the recursion limit** (CPython: 1000
  frames). A top-down solve on a 10⁵-length input is not slow, it is *broken*.
- **You want the space optimisation.** [[rolling-variables]] falls out of a table
  naturally and is awkward from a memo.
- **Every state is reachable anyway**, so there is nothing to lose by computing
  all of them.
- **The interviewer asked for the final answer**, not the first one. A memo is a
  good first answer; a table is a good last answer.

## How it works

A row of buckets on a bench, filled left to right:

```
i:     0     1     2     3     4     5
     ┌────┬────┬────┬────┬────┬────┐
dp:  │  0 │  1 │  1 │  2 │  3 │  5 │
     └────┴────┴────┴────┴────┴────┘
       ▲    ▲    │
       └────┴────┘   each bucket is filled from the two behind it
```

```python
def fib(n):
    dp = [0] * (n + 1)          # size n+1 so dp[n] is a real slot
    dp[1] = 1                   # base cases, written directly into the table
    for i in range(2, n + 1):
        dp[i] = dp[i-1] + dp[i-2]
    return dp[n]
```

Mechanical derivation from the memo version: the base cases become the
pre-filled cells, the recursive calls become reads of earlier cells, and the
recursion is replaced by a loop.

## Fill order — the only part you have to think about

**Fill in an order where every cell the recurrence reads is already written.**

That is the entire rule, and it is question 4 of the four questions in
[[state-transition]]. If the recurrence reads backwards (`dp[i-1]`,
`dp[i][j-1]`, `dp[i-1][j-1]`) then increasing order works — rows
top-to-bottom, columns left-to-right. If it reaches *forward* (`dp[i+1]`,
`dp[i][j+1]`) you iterate backwards instead.

Getting this wrong does not crash. It reads a plausible-looking zero from a cell
that hasn't been filled yet and returns a wrong answer quietly. This is the
failure mode to test for.

**Memoization sidesteps fill order entirely** — the call stack discovers a valid
order for you. That convenience is exactly what you are giving up here, and it
is the main reason a table takes longer to write under pressure.

## The trade against memoization

| | Top-down memo | Bottom-up table |
|---|---|---|
| Ease of writing | **Easier** — recursion plus two lines | Harder — you must order the fill yourself |
| Wasted work | **Computes only reachable states** | Computes **every** state, reachable or not |
| Stack | O(depth) frames; can hit the recursion limit | **None** |
| Space optimisation | Awkward | **Natural** — [[rolling-variables]] fall out |
| Reconstructing the answer | Awkward | Easy — walk back through the table |
| Interview role | Good first answer | **Good final answer** |

The honest summary: you trade *"I only compute what I need"* for *"I never
recurse and I can throw the old rows away."* On a dense problem where
everything is reachable that trade is free. On a sparse one — a huge state space
where only a thin slice is ever visited — the memo genuinely wins, and saying so
is worth points.

**Under time pressure:** write the memo, get it correct, then say *"and this
converts to bottom-up, which lets me drop the stack and roll the space down."*
Convert if the clock allows.

## Complexity

Same as the memo — **states × work per state** — minus the O(depth) stack term.
Space is O(states) for the table, and often reducible from there.

## Gotchas

- **Off-by-one on the table size.** `[0] * (n + 1)`, not `[0] * n`, so `dp[n]`
  is a real slot.
- **`[[0] * n] * m` for a 2-D table.** The outer `*` copies the *reference*:
  every row is the same list, writes land everywhere, and nothing looks wrong.
  Use `[[0] * n for _ in range(m)]`. Always.
- **Base cases invented to make the loop tidy** rather than read off the problem
  statement. Get these wrong and everything downstream is wrong.
- **Returning the wrong cell.** `dp[-1]` if the state means "answer for the
  whole input"; `max(dp)` if it means "answer *ending exactly here*". Decide
  which when you define the state, not at the end.
- **Wrong "impossible" sentinel.** A minimisation table needs a value that
  cannot win (`float('inf')`, or `target + 1`), not `0`.

## Related

- [[dynamic-programming]] — the parent idea; this is its bottom-up half
- [[memoization]] — the other direction; easier to write, costs you a call stack
- [[state-transition]] — the four questions; question 4 *is* fill order
- [[rolling-variables]] — what to do once you notice most of this table is dead
- [[dp-grid]] — the 2-D table where fill order is literally a direction of travel
- [[loop-invariant-bounds]] — the same discipline of stating what is true of the
  data behind the cursor before writing the loop

## Evidence

Problems where I used this unaided:

*None yet.*
