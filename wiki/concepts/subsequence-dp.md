---
type: concept
status: unseen
updated: 2026-09-14
sources: [https://www.geeksforgeeks.org/dynamic-programming/]
tags: [algorithms, 2d-dynamic-programming]
---

# Two-Sequence DP

**In one sentence, in my own words** — two strings in and a question about how
they relate, so the state is a *pair of prefix lengths*: `dp[i][j]` is the answer
for the first `i` of A and the first `j` of B, and each cell asks one question —
do the last two characters match?

Pattern B in [[../lessons/14-2d-dynamic-programming|Lesson 14]]. The most
reusable artefact in the whole DP track: edit distance, longest common
subsequence, longest common substring, distinct subsequences, interleaving
strings and regex matching are all this one table with different cell contents.

## When to reach for it

> **Two strings or two arrays given as input, and a question about how they
> relate.**

That is the single loudest signal in DSA — almost nothing else produces that
shape. Reinforcing words: "common", "match", "interleave", "transform A into B",
"subsequence", "distinct ways to form", "align".

Feels like a trick the first three times and then never feels like a trick again.

## The shape — memorise the shape, not any one recurrence

> `dp[i][j]` = the answer for **the first `i` characters of A** and
> **the first `j` characters of B**.

`i` and `j` are **counts, not indices**. `dp[2][3]` is about `A[:2]` and `B[:3]`.
There is no physical grid here — the "grid" is the space of pairs of prefixes.

### Why the +1 row and column exist

The table is `(m+1) × (n+1)`. Row 0 means "A contributes nothing", column 0 means
"B contributes nothing" — the **empty prefix**. Every problem in this family has
a trivially known answer when one side is empty (LCS of anything with `""` is 0;
edit distance from `""` to a length-`j` string is `j`), so **row 0 and column 0
are your base cases, free, with no special-casing inside the loop.**

Without the offset you would write `if i == 0` guards in the hot path and get one
of them wrong.

### The cost of the offset — where people bleed time

**The character cell `dp[i][j]` is about is `A[i-1]` and `B[j-1]`, not `A[i]` and
`B[j]`.** Write it down. Say it out loud when you set the table up. Almost every
bug in this pattern is that one subtraction.

```python
for i in range(1, m + 1):          # row i is about a[i-1]
    for j in range(1, n + 1):
        if a[i - 1] == b[j - 1]:   # the -1 that causes every bug in this topic
            ...
```

### The match / no-match branch

With `i` and `j` fixed you are deciding what to do with the **last character of
each prefix**. Exactly two worlds:

- **They match** (`A[i-1] == B[j-1]`) → consume the pair together; the rest of
  the answer is whatever was true for `A[:i-1]` and `B[:j-1]`, i.e. the
  **diagonal** `dp[i-1][j-1]`. *Match ⇒ look diagonally back.* It is the only
  move that consumes from both strings at once.
- **They don't match** → give up on one side's last character. Dropping A's
  leaves `dp[i-1][j]` (**above**); dropping B's leaves `dp[i][j-1]` (**left**).
  Take `max` when building something up, `min` when paying a cost.

> **Diagonal is the "they agree" move; up and left are the "sacrifice one"
> moves.**

Every variant differs only in what goes in the cell and what the no-match branch
costs. Get that sentence solid and the family collapses into one thing.

## Hand-fill a 4×4 when stuck

Four minutes of arithmetic beats twenty minutes of staring at a recurrence.
[[../lessons/14-2d-dynamic-programming|Lesson 14]] §3c walks one cell by cell;
the answer shows up as a **diagonal chain** drawn in the table, which is the
clearest picture of why the match branch reads the diagonal.

## Where the answer lives

Two possible meanings for a cell, and they have different return statements:

| Cell means | Answer is |
|---|---|
| "answer for the whole pair of prefixes" | **bottom-right**, `dp[m][n]` |
| "answer *ending exactly here*" (contiguous variants) | **max over the whole table** |

Decide which one your cell means when you define the state — getting it wrong
produces code that is right everywhere except the return statement.

## Complexity

- States O(m × n), work per state O(1) → **time O(m × n)**
- Space O(m × n) full table; **O(min(m, n))** with
  [[rolling-variables|rolled rows]], putting the longer string on the rows
- Brute force: O(2^(m+n)) — branch on "drop from A" / "drop from B" at every
  step. State this first; the table removes the exponent *only* because the
  brute force recomputes the same `(i, j)` over and over

**Rolling costs you reconstruction.** Without the table you cannot walk back to
recover the actual subsequence or alignment — only the number survives. If the
problem asks for the string itself, keep the table.

## Gotchas

- **`A[i]` where you meant `A[i-1]`.** The permanent off-by-one. Write the
  meaning of `dp[i][j]` as a comment above the loop before writing the loop.
- **Sizing the table `m × n`.** No empty prefix, so base cases move into the loop
  as guards. Pay the extra row and column.
- **`[[0] * (n+1)] * (m+1)`.** All rows are the same list. Use a comprehension.
- **Slicing inside a memoised recursion.** Pass indices — slices are unhashable
  in `lru_cache` and O(k) per call, which silently destroys the complexity.
- **Substring vs subsequence.** Contiguous means a mismatch **resets to 0**, not
  "take the max of the neighbours". Different problem, same table.
- **Empty-input edges.** One or both strings empty. The +1 offset handles these
  for free — which is exactly why you should test them, to confirm the base cases
  really are right.

## Related

- [[dp-grid]] — the other 2-D story, where the axes are literally space; same
  machinery, but there the state design is free
- [[state-transition]] — the four questions; this pattern is what "design the
  state" looks like when the problem doesn't hand you one
- [[bottom-up-tabulation]] — the table; or go top-down with [[memoization]] over
  `(i, j)` and let the call stack find the fill order
- [[rolling-variables]] — two rows instead of m+1
- [[two-pointers]] — the intuition for consuming two sequences in step; this is
  what you need when a single greedy pass over both is not enough

## Evidence

Problems where I used this unaided:

*None yet.*
