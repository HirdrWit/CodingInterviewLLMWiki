---
type: concept
status: unseen
updated: 2026-09-14
sources: [https://www.geeksforgeeks.org/dynamic-programming/]
tags: [algorithms, 2d-dynamic-programming]
---

# DP Over a Grid

**In one sentence, in my own words** — the state is a literal square `(i, j)` on
a floor, movement is restricted (usually down-and-right only), and each cell's
answer is accumulated from the neighbours you could have arrived from.

Pattern A in [[../lessons/14-2d-dynamic-programming|Lesson 14]]. The easier of
the two stories about why a state has two axes — do this one first, because the
state design is handed to you and you get a clean rep on table setup and fill
order.

## When to reach for it

Trigger phrases, and they are unusually loud:

- "an m × n grid", "a robot starts at the top-left and must reach the
  bottom-right"
- **"you may only move down or right"** — the load-bearing sentence
- "number of distinct paths", "minimum cost path", "largest square of 1s"

**The tell that it's DP and not BFS:** you are asked to *count* paths or
*optimise over all* paths, not to find one. Counting and optimising accumulate;
finding searches. If the question is "is there a path" or "shortest path in an
unweighted grid with free movement", that's [[bfs-graph]] over an
[[implicit-graph]], not a table.

**Why the movement restriction matters so much:** down-and-right-only means you
can never revisit a square, so the dependency structure is acyclic. That is
exactly what makes a plain table work. Lift the restriction and there are cycles,
and a table has no valid fill order any more.

## How it works

```
        j-1    j
      ┌──────┬──────┐
 i-1  │  ↖   │  ↑   │
      ├──────┼──────┤
  i   │  ←   │ dp   │    dp[i][j] is built from at most
      └──────┴──────┘    dp[i-1][j-1], dp[i-1][j], dp[i][j-1]
```

1. **State.** `dp[i][j]` = the answer for the subproblem *ending at square
   `(i, j)`* — ways to reach it, cheapest cost to reach it, biggest square with
   its corner there. Whatever the problem asks, said in one sentence first.
2. **Recurrence.** Exactly the neighbours you can legally enter from.
3. **Base cases.** The **edges with a missing neighbour** — the top row and the
   left column, where there is only one way in.
4. **Order.** Top-to-bottom, left-to-right. Every neighbour is then already
   computed when you reach it.

**Counting and optimising are the same table with a different operator.** "How
many ways" sums the incoming neighbours; "cheapest way" takes their `min` and
adds the cell's own cost. One character of difference.

The real work in a grid problem is not the recurrence — it is **reading the
movement rules out of the statement** and noticing which edges are special.

## Complexity

- States: O(m × n), one per square
- Work per state: O(1) — a `max`/`min`/`+` over at most three neighbours
- **Time: O(m × n).** States × work per state, as always
- Space: O(m × n) for the full table, **O(min(m, n))** after
  [[rolling-variables|rolling rows]] — at the cost of not being able to walk
  back through the table to reconstruct the actual path

Brute force for comparison, and say it first: recurse from the start square
branching down and right — O(2^(m+n)), exponential *only* because it re-solves
the same `(i, j)` over and over.

## Gotchas

- **`dp = [[0] * n] * m`.** The outer `*` copies the *reference*: all m rows are
  the same list, so writing one cell writes every row. Silent, and the most
  expensive five characters in DP. Use `[[0] * n for _ in range(m)]`.
- **Base-case edges left to the loop.** Pre-fill the top row and left column
  before the double loop rather than guarding inside it.
- **Blocked cells / obstacles.** Their value is 0 ways (or `inf` cost), and they
  must be set *before* being read as a neighbour.
- **Degenerate inputs.** 1×1 and 1×n grids. Test them — they are exactly where a
  wrong base case shows up.
- **Returning the wrong cell.** Bottom-right if the cell means "answer for the
  whole journey"; max over the table if it means "answer ending here" (the
  largest-square variant).
- **`math.comb` exists** and some grid-counting problems have a closed form.
  Mentioning it *after* giving the DP is a nice flex; leading with it dodges the
  question that was asked.

## Related

- [[subsequence-dp]] — the other 2-D story: the axes are two sequences, not
  space. Same table machinery, much harder state design
- [[state-transition]] — the four questions; here question 1 is free and
  question 2 is read out of the movement rules
- [[bottom-up-tabulation]] — the table and its fill order
- [[rolling-variables]] — two rows instead of m
- [[bfs-graph]] — what a grid problem becomes when movement is unrestricted, or
  when you want *a* path rather than a count over all of them
- [[implicit-graph]] — the same "cells are nodes" reframing, searched rather than
  accumulated

## Evidence

Problems where I used this unaided:

*None yet.*
