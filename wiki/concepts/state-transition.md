---
type: concept
status: unseen
updated: 2026-09-14
sources: [https://www.geeksforgeeks.org/dynamic-programming/]
tags: [algorithms, 1d-dynamic-programming, 2d-dynamic-programming]
---

# State & Transition

**In one sentence, in my own words** — decide precisely what `dp[i]` *means* in
one English sentence, then write how it is built from smaller answers; the code
is what's left over once those two are on the page.

The four-question procedure from [[../lessons/13-1d-dynamic-programming|Lesson 13]]
§5. The part of [[dynamic-programming]] that actually transfers — ten memorised
recurrences get you ten problems, a procedure gets you the eleventh.

## When to reach for it

Every DP problem, before any code. Also the thing to run when a DP is *stuck*:
a recurrence that won't close is almost always a state that is too small, not a
recurrence that is too hard.

## The four questions

```
1. STATE      — what does dp[i] MEAN? Finish this sentence precisely:
                "dp[i] is the <best/count/bool> for the first i <things>,
                 ending at / using at most / considering only ..."
2. RECURRENCE — how is dp[i] built from smaller answers?
                What are the CHOICES available at step i?
3. BASE CASES — the smallest inputs answerable with no help at all.
                Read off the PROBLEM STATEMENT, not guessed from the recurrence.
4. ORDER      — which fill direction has every dependency ready when needed?
```

Write the answers down. They are ~80% of the solve and they are exactly what you
should be narrating to an interviewer.

## 1. State — the question everyone skips

**If you cannot finish the sentence "dp[i] is…" in plain English, you do not yet
have a solution, and more code will not fix it.** A vague state produces a
recurrence you cannot verify, which produces a bug you cannot find.

Two flavours that get mixed up constantly, and mixing them is a classic
self-inflicted wound:

| State | Recurrence shape | Answer lives at |
|---|---|---|
| "best **considering the first i**" | `dp[i] = max(dp[i-1], …)` — carries the best so far | `dp[-1]` |
| "best **ending exactly at i**" | `dp[i]` built by extending compatible earlier `j` | `max(dp)` |

Same problem, different states, different code, different return statement.
Decide which one you mean *when you define it*, not at the end.

**The state is not always an index into the input.** It can be a target value
(`dp[t]` = best way to make amount `t`), a pair of prefix lengths
(see [[subsequence-dp]]), a square on a floor (see [[dp-grid]]), or an index
paired with a remaining budget.

**How to know you need a second dimension.** Ask: *"I'm partway through. What is
the smallest set of numbers that tells me everything I need to finish?"* If the
honest answer needs two numbers — position *and* remaining budget, progress in A
*and* progress in B — the DP is 2-D. And if you find yourself in a 1-D solution
passing extra context around in a variable that isn't the loop index, **that
variable is your missing second dimension.**

**How to know your state is too small.** The recurrence won't close — you can't
compute `dp[i]` from the previous answers alone. The fix is always to widen the
state, never to bolt on a special case. (Maximum Product Subarray is the
textbook case: products aren't monotone, so the running *minimum* is a live
candidate for the next maximum, and the state has to carry both.)

## 2. Transition — enumerate the choices

The recurrence is the answer to *"what choices do I have at step i, and what
subproblem does each one leave me with?"* Name them explicitly:

- take it / skip it → `dp[i] = max(take, skip)`
- consume one unit / consume two → `dp[i] = dp[i-1] + dp[i-2]`, each branch
  guarded by a validity check
- characters match / don't match → diagonal, or the better of up and left

The operator encodes the question: **`+` counts, `max`/`min` optimises, `or`
answers reachability.** Same skeleton, one character different.

## 3. Base cases — read them, don't invent them

The smallest inputs you can answer with **no help at all**, taken from the
problem statement. "The LCS of anything with the empty string is 0." "Turning
`""` into a length-`j` string costs `j` inserts."

The failure mode is inventing base cases that make the recurrence look tidy.
They will be wrong, and everything above them inherits the error.

## 4. Order — see [[bottom-up-tabulation]]

Fill in an order where every cell the recurrence reads is already written. If
you go top-down with [[memoization]], the call stack finds an order for you and
this question disappears.

## Complexity falls out of the state

Once the state is defined, the complexity is arithmetic:
**time = number of states × work per state.** Noticing that the work per state
is an inner loop is often how you discover the state was wrong in the first
place.

## Gotchas

- Writing code before the state is a sentence. The expensive one.
- "In the first i" vs "ending at i" — pick deliberately, then honour it in the
  return statement.
- Base cases reverse-engineered from the recurrence.
- Widening the state with a special case instead of an extra dimension.
- Claiming polynomial time when the state is indexed by a numeric *value* —
  that is pseudo-polynomial, and interviewers probe it.

## Related

- [[dynamic-programming]] — the parent idea; this is its method
- [[memoization]] / [[bottom-up-tabulation]] — the two ways to execute a
  recurrence once you have one; neither helps you *find* one
- [[rolling-variables]] — a direct consequence of how far back the transition reaches
- [[dp-grid]] / [[subsequence-dp]] — the two stories about why a state needs two axes
- [[decision-tree]] — the tree of choices the transition enumerates; DP is
  [[backtracking]] over that tree with the answers kept

## Evidence

Problems where I used this unaided:

*None yet.*
