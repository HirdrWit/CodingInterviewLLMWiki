---
type: concept
status: unseen
updated: 2026-09-14
sources: []
tags: [algorithms, 1d-dynamic-programming, 2d-dynamic-programming]
---

# Rolling Variables

**In one sentence, in my own words** — if the recurrence only ever reaches back
`k` steps, every cell older than `k` is dead weight, so throw the table away and
keep `k` variables instead: O(n) space becomes O(1).

Level 4 of the levels in [[../lessons/13-1d-dynamic-programming|Lesson 13]] §3.
The last mechanical step after [[bottom-up-tabulation]].

## When to reach for it

The rule is mechanical, and that's the point — **it is not a new idea, it is an
observation about a recurrence you already wrote**:

> Look at the loop body. Which cells does it read? If the answer is a fixed
> small set of recent ones, you need that many variables and nothing else.

Trigger phrases in the statement don't exist for this one; the trigger is in
*your own code*. Several Blind 75 problems are graded on whether you spot it
unprompted, so the habit is: finish the table, state its complexity, then
immediately ask "what does the loop actually read?"

## How it works

The table version reads only `dp[i-1]` and `dp[i-2]`; the other n−3 buckets are
never touched again.

```python
def fib(n):
    prev, curr = 0, 1
    for _ in range(n - 1):
        prev, curr = curr, prev + curr
    return curr
```

`prev, curr = curr, prev + curr` — the right-hand side is evaluated fully before
anything is assigned, so there is no temp variable and no ordering bug. This is
the one Python idiom to have in your fingers for this pattern.

Two rolling variables for a reach of 2; one for a reach of 1; three for a reach
of 3. Name them for their *meaning* (`rob_prev` / `rob_curr`, `best_with` /
`best_without`), not `a` and `b` — an interviewer reading `a, b = b, a + b` has
to reverse-engineer what you meant.

## Rolling rows — the 2-D case

The same observation one dimension up. A recurrence that reads `dp[i-1][j-1]`,
`dp[i-1][j]` and `dp[i][j-1]` never touches row `i-2`, so **two rows is enough**:

```python
prev = [0] * (n + 1)
for i in range(1, m + 1):
    curr = [0] * (n + 1)
    for j in range(1, n + 1):
        curr[j] = ...          # reads prev[j-1], prev[j], curr[j-1]
    prev = curr
return prev[n]
```

O(m × n) → **O(min(m, n))** — `min`, because you get to choose which input is
the rows, so put the longer one there and keep the rows short:

```python
if len(a) < len(b):
    a, b = b, a
```

## What it costs you

**You lose the table, so you lose the ability to reconstruct the answer.** Only
the numeric result survives — if the problem wants the actual subsequence, the
actual path, or the actual edit sequence, you must keep the full table. State
this trade out loud when you offer the optimisation; offering it *with* its cost
is what reads as fluency rather than recall.

## Complexity

Time is **unchanged** — same states, same work per state. Only space moves:
O(n) → O(k), or O(m × n) → O(min(m, n)).

## Gotchas

- **Rolling to a single row without handling the diagonal.** In the one-array
  version, `dp[j-1]` has already been overwritten with the *current* row by the
  time you read it, so the "diagonal" you read is wrong. Stash the old value
  first, or iterate `j` backwards. This is the classic knapsack space trick and
  a genuine source of silent wrong answers. **Two explicit rows is the version
  to write under pressure.**
- **Updating in the wrong order** in the 1-D case. Use the tuple swap; a
  sequence of separate assignments clobbers a value you still need.
- **Claiming "O(n) space" after rolling rows without saying which n.** It is
  O(min(m, n)) *only* if the longer input is on the rows. Say the whole thing.
- **Optimising too early.** A correct table beats a broken pair of variables.
  Get the recurrence right, then roll.
- **Rolling when the recurrence doesn't allow it** — an inner loop that scans
  *all* earlier states (best-subsequence-ending-here, unbounded knapsack over a
  target) genuinely needs the whole table. Check what the loop reads; don't
  assume.

## Related

- [[bottom-up-tabulation]] — the table this optimisation deletes; rolling is
  natural from a table and awkward from a [[memoization|memo]]
- [[state-transition]] — the reach of the transition is what decides `k`
- [[dynamic-programming]] — the parent idea
- [[sliding-window]] — the same instinct outside DP: carry a summary of the
  recent past instead of storing it
- [[fast-slow-pointers]] — likewise, O(1) state standing in for a data structure

## Evidence

Problems where I used this unaided:

*None yet.*
