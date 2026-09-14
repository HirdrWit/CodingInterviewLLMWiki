---
type: lesson
topic: 1d-dynamic-programming
status: ready
updated: 2026-09-14
sources: [https://neetcode.io/practice/practice/blind75, https://www.geeksforgeeks.org/dynamic-programming/, https://www.geeksforgeeks.org/overlapping-subproblems-property-in-dynamic-programming-dp-1/, https://www.geeksforgeeks.org/optimal-substructure-property-in-dynamic-programming-dp-2/, https://docs.python.org/3/library/functools.html]
tags: [lesson, 1d-dynamic-programming]
---

# Lesson 13 — 1-D Dynamic Programming

**Curriculum:** [[../curriculum/1d-dynamic-programming|Topic 13]] · **Materials:** [[../meta/resources|Resources]]
**Prerequisites:** recursion, and [[../curriculum/backtracking|Topic 9 — Backtracking]]. You need to
be comfortable with a function that calls itself before any of this lands.
**Work through in:** 2–3 hours, in two sittings. Do not do this one tired.

---

## 1. Why this topic

The obvious reason: 10 problems, the second-biggest topic in the list, and DP
shows up in a large share of Medium/Hard screens.

The real reason: **this is where most people stall, and they stall for a reason
that has nothing to do with intelligence.** They stall because they were taught
DP as a bag of tricks — "House Robber is the two-variable one, Coin Change is
the loop-inside-a-loop one" — and a bag of tricks does not survive a problem you
haven't seen. Ten memorised recurrences get you ten problems. A *procedure* gets
you the eleventh, which is the one the interviewer will actually ask.

So the deliverable of this lesson is not ten recurrences. It's one repeatable
four-question procedure (section 5) that you run on any DP problem, including
ones invented on the spot.

The connection back: **DP is [[../curriculum/backtracking|Topic 9]] with a
memory.** Backtracking explores a tree of choices and throws the work away.
Dynamic programming explores the same tree and *keeps* the answers, because the
same subproblem keeps showing up. That's it. That's the whole idea. Everything
else in this lesson is bookkeeping around that sentence.

---

## 2. The mental model

### The photocopier that never notices

Imagine an office where every clerk, given a job, splits it into two smaller
jobs and hands them to two more clerks. Nobody talks to anybody else. Nobody
writes anything down.

Ask the office for `fib(5)`. Here is what actually happens:

```
                          fib(5)
                   ┌────────┴────────┐
                fib(4)             fib(3)
             ┌────┴────┐         ┌───┴───┐
          fib(3)     fib(2)    fib(2)  fib(1)
         ┌──┴──┐     ┌─┴─┐     ┌─┴─┐
      fib(2) fib(1) fib(1) fib(0) ...
      ┌─┴─┐
   fib(1) fib(0)
```

Count the `fib(2)` nodes: **three of them**, each running the full subtree
underneath. `fib(3)` appears twice. The tree has roughly 2ⁿ nodes, but there are
only **n distinct questions in it**. The office is computing the same answers
over and over because nobody kept a note.

Now put a whiteboard on the wall. First clerk to compute `fib(2)` writes the
answer up. Every clerk checks the whiteboard before starting work.

```
                          fib(5)
                   ┌────────┴────────┐
                fib(4)             fib(3) ← whiteboard hit, O(1)
             ┌────┴────┐
          fib(3)     fib(2) ← whiteboard hit
         ┌──┴──┐
      fib(2)  fib(1)
      ┌─┴─┐
   fib(1) fib(0)
```

The tree collapses to a **path**. 2ⁿ becomes n. One whiteboard, exponential
speedup. That whiteboard is [[../concepts/memoization|memoization]], and that
collapse is the entire subject.

### The two properties that make it work

You are allowed to put up the whiteboard only when two things are true, and
interviewers ask about both by name:

- **Overlapping subproblems** — the same subquestion is asked more than once.
  If every subproblem is distinct, a memo is just wasted memory. (Merge sort
  splits into subproblems too, but never the *same* one twice — so merge sort is
  divide-and-conquer, not DP. Knowing this distinction is worth real points.)
- **Optimal substructure** — the best answer to the big problem is built out of
  the best answers to the smaller ones. If knowing the optimal `f(4)` doesn't
  help you get `f(5)`, there is nothing to memoise.

### Physical analogy for the bottom-up flip

Memoization is top-down: you ask the big question and it *pulls* the small
answers it needs. The other direction is to *push* — start at the smallest
question you can answer without help, and fill forward until you reach the one
that was asked.

That's a row of buckets on a bench:

```
i:     0     1     2     3     4     5
     ┌────┬────┬────┬────┬────┬────┐
dp:  │  0 │  1 │  1 │  2 │  3 │  5 │
     └────┴────┴────┴────┴────┴────┘
       ▲    ▲    │
       └────┴────┘
        each bucket is filled from the two behind it
```

Fill left to right; when you reach the end, the answer is sitting there. That's
[[../concepts/bottom-up-tabulation|bottom-up tabulation]]. Same recurrence, same
answers, opposite direction of travel.

---

## 3. The mechanics

Three levels of the same computation. Write the first, mechanically derive the
others. **Never try to invent level 3 directly** — that's the mistake that eats
the 30-minute box.

**Level 1 — the naive recursion.** Correct, unusably slow. Its only job is to
get the recurrence out of your head and onto the page.

```python
def fib(n):
    if n < 2:              # base case
        return n
    return fib(n-1) + fib(n-2)     # recurrence
```

O(2ⁿ) time, O(n) stack. The recursion tree above is why.

**Level 2 — add the memo.** Change nothing about the logic. Add a dict, check it
on the way in, fill it on the way out.

```python
def fib(n, memo={}):
    if n < 2:
        return n
    if n in memo:                   # whiteboard check
        return memo[n]
    memo[n] = fib(n-1, memo) + fib(n-2, memo)
    return memo[n]
```

O(n) time, O(n) space. The reasoning for "O(n) time" is the one to say aloud:
**there are n distinct states, each is computed exactly once, and each costs O(1)
work on top of its recursive calls.** That sentence — *number of states × work
per state* — is how you state the complexity of any DP, and it is the single
most useful thing in this section.

**Level 3 — flip it bottom-up.** Replace recursion with a loop that fills the
table in dependency order.

```python
def fib(n):
    dp = [0] * (n + 1)
    dp[1] = 1
    for i in range(2, n + 1):
        dp[i] = dp[i-1] + dp[i-2]
    return dp[n]
```

Same O(n) time and space, but no call stack — no recursion limit, no stack
frames, and usually a decent constant-factor win.

**Level 4 — drop to rolling variables.** Look at the loop body: it only ever
reads `dp[i-1]` and `dp[i-2]`. The other n−3 buckets are dead weight.

```python
def fib(n):
    prev, curr = 0, 1
    for _ in range(n - 1):
        prev, curr = curr, prev + curr
    return curr
```

O(n) time, **O(1) space**. See [[../concepts/rolling-variables|rolling variables]].
The rule is mechanical: *if the recurrence reaches back at most k steps, you need
at most k variables.* Several problems in this topic are graded on whether you
spot that.

### Memo or table? The honest trade

| | Top-down memo | Bottom-up table |
|---|---|---|
| Ease of writing | **Easier** — it's the recursion plus two lines | Harder — you must order the fill yourself |
| Wasted work | **Computes only reachable states** | Computes every state, reachable or not |
| Stack | O(depth) frames; can blow the recursion limit | **None** |
| Space optimisation | Awkward | **Natural** — rolling variables fall out |
| Interview default | Good first answer | **Good final answer** |

Practical advice for the box: **write the memo version, get it correct, say
"and this converts to bottom-up, which lets me drop to O(1) space" — then do the
conversion if there's time.** A correct memo beats a half-written table every
time.

---

## 4. Complexity

| Version | Time | Space | Why |
|---|---|---|---|
| Naive recursion | O(2ⁿ) *(branching^depth)* | O(n) stack | Recomputes the whole subtree at every node |
| Memoized | **O(states × work-per-state)** | O(states) + O(depth) stack | Each state computed once |
| Tabulated | O(states × work-per-state) | O(states) | Same, no stack |
| Rolling variables | O(states × work-per-state) | **O(1) or O(k)** | Only the last k states are ever read |

The one formula: **time = number of states × work done at each state.**

- 1-D state, O(1) transition → **O(n)**. (Climbing Stairs, House Robber, Decode Ways.)
- 1-D state, O(n) transition (inner loop over all earlier states) → **O(n²)**.
  (Longest Increasing Subsequence the straightforward way, Word Break.)
- State is *amount*, transition loops over the coin set → **O(amount × coins)**.
  Note this is **pseudo-polynomial**, not polynomial: it scales with the
  numeric *value* of the input, not its length. Interviewers do probe this.

Say the formula out loud when you state complexity. "There are n states and each
does O(n) work scanning earlier states, so O(n²)" is a much stronger answer than
"it's O(n²)".

---

## 5. The patterns

### The four questions — run these on every DP problem

Before any code. Write the answers down; they're 80% of the solve, and they're
also exactly what you should be narrating to an interviewer.

```
1. STATE      — what does dp[i] MEAN?  Finish this sentence precisely:
                "dp[i] is the <best/count/bool> for the first i <things>,
                 ending at / using at most / considering only ..."
2. RECURRENCE — how is dp[i] built from smaller answers?
                What are the CHOICES available at step i?
3. BASE CASES — the smallest inputs you can answer with no help at all.
                Usually i = 0 and i = 1. Get these wrong and everything after
                is wrong.
4. ORDER      — which direction fills the table so every dependency is
                already computed when it's needed?
```

Question 1 is the one people skip and the one that decides everything. A vague
state definition produces a recurrence you can't verify. **If you can't finish
the sentence "dp[i] is…" in plain English, you do not yet have a solution — and
more code will not fix it.** See [[../concepts/state-transition|state transition]].

### Recognising a DP problem at all

Four honest signals. Two or more together and you should be running the four
questions:

> - **"How many ways…"** — count over a tree of choices.
> - **"Minimum / maximum …"** where each step is a *choice* — not a scan, a choice.
> - **"Can I reach / is it possible to …"** — a boolean over reachable states.
> - **The brute force re-explores the same subproblem.** This is the decisive
>   one. Sketch the recursion tree; if the same argument appears at two different
>   nodes, it's DP.

Two anti-signals, worth as much as the signals: if a greedy local rule provably
works, it's greedy (Topic 15), not DP. If the answer depends on a contiguous
window with a monotone rule, it's usually sliding window (Topic 3).

### Pattern A — the Fibonacci recurrence
> **Trigger:** "in how many distinct ways can you reach step n", where each move advances a fixed small amount

The number of ways to arrive somewhere is the sum of the ways to arrive at each
place you could have stepped from. State is a single index; the recurrence
reaches back a fixed 1 or 2 steps; rolling variables apply immediately. This is
the smallest complete DP that exists — use it as the template for the shape of
all the others.

### Pattern B — take-or-skip (linear choice)
> **Trigger:** "maximum total, but you can't pick two adjacent ones"

At each element you have exactly two options, and they lead to *different
subproblems*: take it and jump past its neighbour, or skip it and move on by
one. `dp[i] = max(take, skip)`. The state is "best achievable considering the
first i elements"; note it is **not** "best ending exactly at i" — being precise
about which one you mean is the whole difficulty. Reaches back 2 → two
[[../concepts/rolling-variables|rolling variables]].

### Pattern C — the circular constraint
> **Trigger:** the same problem as B, but "arranged in a circle" / "the first and last are adjacent"

Don't invent a new recurrence. **Break the circle by case-splitting on one
element**, then run the linear solution twice on two ordinary sub-ranges and
take the best. General move worth remembering: *a circular constraint is usually
a linear problem run twice with one element excluded each time.*

### Pattern D — expand around centre
> **Trigger:** "longest palindromic …", "count the palindromic …"

A palindrome is defined by growing outward from a middle, so walk every possible
centre and expand while the characters match. There are 2n−1 centres (n single
characters, n−1 gaps between them — **the even-length case is the one everyone
forgets**), each expansion is O(n), giving O(n²) time and **O(1) space**.

Why this is in a DP lesson: the table version of these problems is real
(`dp[i][j]` = "is s[i..j] a palindrome", built from `dp[i+1][j-1]`), and it is
the same O(n²) time but O(n²) space. **Expand-around-centre is that recurrence
executed in the order that lets you throw the table away** — you compute
`dp[i+1][j-1]` immediately before you need it, so you never store it. Same
overlapping-subproblem structure, same optimal substructure, better constant and
better space. Reach for the centres in the box; know the table exists for the
follow-up question.

### Pattern E — recurrence with a validity check
> **Trigger:** "how many ways to decode/parse/split", where each step consumes one *or* two units and some consumptions are illegal

Pattern A, plus a guard. `dp[i] = (dp[i-1] if the 1-unit read is valid) + (dp[i-2] if the 2-unit read is valid)`.
The DP is easy; **the validity predicate is where the problem lives**, and it is
where the edge cases live too. Write that predicate as its own named helper and
test it separately before wiring it into the recurrence.

### Pattern F — unbounded knapsack over a target
> **Trigger:** "fewest items summing to exactly T", with unlimited reuse of each item

The state is not an index into the input — **it's the target value**. `dp[t]` =
best way to make amount `t`. For each `t`, try every item and look back at
`dp[t - item]`. O(T × items). Two details that decide correctness: initialise
"impossible" with something that can't win (`inf`, or `T+1`), and check
`t - item >= 0` before looking back. This is the first problem in the topic
where the state isn't a position in the input, and that shift is the lesson.

### Pattern G — carry a second quantity (max *and* min)
> **Trigger:** "maximum product subarray", or any objective where a bad value can become a good one

Sums are monotone; products are not. A large negative times a negative becomes a
large positive, so the *minimum* so far is a live candidate for the next
maximum. The fix is to widen the state: track running max **and** running min at
each position, and derive both from both. The transferable idea: **when the
recurrence can't be computed from the previous answer alone, the state is too
small — add to it.** That question — "is my state sufficient?" — is the one to
ask whenever a recurrence won't close.

### Pattern H — reachability over positions
> **Trigger:** "can the string be segmented into…", "is it possible to reach the end"

`dp[i]` is a **boolean**: "is position i reachable". Start with `dp[0] = True`,
and for each reachable position, mark everything one valid move away as
reachable too. Counting and optimising DP get the attention, but boolean
reachability DP is common and often the easier sell in an interview because the
state definition is so hard to get wrong.

### Pattern I — best subsequence ending here
> **Trigger:** "longest increasing subsequence", "longest chain where each element relates to the previous"

State: `dp[i]` = the best answer **ending exactly at i** (not "in the first i" —
this is the other flavour from Pattern B, and mixing them up is a classic
self-inflicted wound). To compute `dp[i]`, scan every `j < i` and extend the
best compatible one. O(n²), n states × O(n) work. The answer is `max(dp)`, not
`dp[-1]` — the best subsequence need not end at the last element.

There is also an O(n log n) version that maintains a list of the smallest
possible tail for each achievable length and binary-searches it (the "patience
sorting" trick). **Get O(n²) right first.** O(n²) plus a clear sentence about
the O(n log n) approach beats a broken O(n log n).

---

## 6. Python notes

```python
from functools import cache        # 3.9+;  lru_cache(None) on older

@cache                             # memoization for free, on any pure function
def f(i):
    if i < 2:
        return i
    return f(i-1) + f(i-2)

dp = [0] * (n + 1)                 # table; size n+1 so dp[n] is a real slot
dp = [False] * (n + 1)             # reachability table
dp = [float('inf')] * (amount + 1) # "impossible" sentinel for a minimisation

prev, curr = curr, prev + curr     # rolling update — RHS evaluates first, so
                                   # no temp variable and no ordering bug
```

Three facts worth knowing:

- **`@cache` is a legitimate interview answer**, but say what it's doing — "this
  memoizes on the arguments" — rather than letting it look like magic. Be ready
  to write the dict by hand if asked; some interviewers will ask exactly that.
- **Default recursion limit is 1000.** A top-down solution on a 10⁵-length input
  will `RecursionError`. That alone is a reason to know the bottom-up flip.
- **Mutable default arguments (`memo={}`) persist between calls.** Handy in a
  script, a bug in production, and worth flagging aloud if you use it.

---

## 7. Traps

- **Coding before the state is defined in English.** The most expensive mistake
  in this topic by a distance. If you can't say what `dp[i]` means in a sentence,
  stop typing.
- **"First i" versus "ending at i".** Two different states, two different
  recurrences, two different answers (`dp[-1]` versus `max(dp)`). Pick one
  deliberately and write it in a comment above the loop.
- **Off-by-one on table size.** If `dp[n]` is the answer, the list has n+1 slots.
- **Base cases guessed rather than derived.** Ask "what is the answer for the
  empty input? for a single element?" and answer those *from the problem
  statement*, not from the pattern of the recurrence.
- **Wrong fill order.** A bottom-up loop that reads a cell it hasn't filled yet
  reads a zero and silently returns a wrong answer. No crash, no clue.
- **Overwriting rolling variables in the wrong order.** Use Python's tuple swap,
  or you'll feed the new value back into the same expression.
- **Missing the even-length palindrome centres.** Half of all
  expand-around-centre bugs.
- **Confusing "impossible" with zero.** In a minimisation, an unreachable state
  initialised to 0 wins every comparison and poisons the whole table. Use `inf`.
- **Claiming polynomial time on a value-indexed DP.** O(amount × coins) is
  pseudo-polynomial. Say so before they ask.
- **Recursion limit on large inputs.** Mentioned above; it's a real failure mode,
  not a theoretical one.

---

## 8. Worked example

Outside the problem set, so nothing is spoiled. It rhymes with things in this
topic — in 1-D DP everything rhymes, which is exactly why the *procedure* is the
thing worth owning.

> **Problem** (LeetCode 746, *Min Cost Climbing Stairs* — not in the Blind 75).
> You're given `cost`, where `cost[i]` is the price of stepping off stair `i`.
> From a stair you may climb one or two stairs. You may start at index 0 or
> index 1. Return the minimum total cost to get past the top.
> `cost = [10, 15, 20]` → `15` (start at 1, pay 15, jump two, done).

**Brute force first, always.** From each stair, recursively try both moves and
take the cheaper. Two branches, depth n → O(2ⁿ). Sketch two levels of the tree
and you see `f(i-2)` appearing under both `f(i-1)` and `f(i)`: **the same
subproblem at two nodes. That's the DP tell.**

**Now the four questions.**

1. **State.** `dp[i]` = the minimum cost to *arrive at* stair `i`. Note "arrive
   at", not "step off" — you pay `cost[i]` when you leave, so the cost of
   arriving at `i` doesn't include `cost[i]`. Getting this sentence exactly
   right is the entire problem; everything below is mechanical.
2. **Recurrence.** You arrived at `i` from `i-1` or from `i-2`. Arriving from
   `i-1` costs `dp[i-1] + cost[i-1]` (you had to pay to leave it). So
   `dp[i] = min(dp[i-1] + cost[i-1], dp[i-2] + cost[i-2])`.
3. **Base cases.** `dp[0] = dp[1] = 0` — you're allowed to start on either for
   free. Straight from the statement, not from the recurrence.
4. **Order.** Every `dp[i]` depends on smaller indices, so fill left to right.
   The answer is `dp[n]`: "past the top" is one step beyond the last stair.

```python
def min_cost_climbing_stairs(cost):
    n = len(cost)
    dp = [0] * (n + 1)                       # dp[i] = min cost to ARRIVE at i
    for i in range(2, n + 1):                # dp[0], dp[1] are 0: free start
        dp[i] = min(dp[i-1] + cost[i-1],
                    dp[i-2] + cost[i-2])
    return dp[n]
```

**Complexity.** n+1 states, O(1) work each → O(n) time, O(n) space.

**Now the space optimisation**, because the recurrence reaches back exactly two:

```python
def min_cost_climbing_stairs(cost):
    one_back = two_back = 0                  # dp[i-1], dp[i-2]
    for i in range(2, len(cost) + 1):
        one_back, two_back = min(one_back + cost[i-1],
                                 two_back + cost[i-2]), one_back
    return one_back
```

O(n) time, **O(1) space**.

**The move to internalise:** the code was the last and smallest step. The solve
happened in question 1, when "minimum cost to arrive at stair i" got pinned down
precisely enough that the recurrence could only be written one way. Run the four
questions on paper before touching the keyboard, every single time.

---

## 9. Materials

Full list on [[../meta/resources|Resources]]. For this lesson, in order:

1. [Overlapping subproblems](https://www.geeksforgeeks.org/overlapping-subproblems-property-in-dynamic-programming-dp-1/) — 10 min. The property that licenses the memo.
2. [Optimal substructure](https://www.geeksforgeeks.org/optimal-substructure-property-in-dynamic-programming-dp-2/) — 10 min. The other half of the licence.
3. [Dynamic programming (hub)](https://www.geeksforgeeks.org/dynamic-programming/) — browse the tabulation-vs-memoization and the classic-problems sections; skip the 2-D material for now, it's Topic 14.
4. [`functools.cache` / `lru_cache`](https://docs.python.org/3/library/functools.html) — 5 min of docs.
5. [NeetCode on YouTube](https://www.youtube.com/@NeetCode) — search the problem name, but **only after** a timed attempt and a debrief.
6. Optional, if the mental model still feels loose: search YouTube for
   MIT 6.006's dynamic programming lectures. Slower and more formal than
   NeetCode, and the better choice if the *why* hasn't clicked.

---

## 10. Self-check

Aloud, in full sentences, before starting the timer. Any that come out mumbled,
go back to section 3.

1. What are the two properties a problem must have for DP to apply? Name a
   recursive algorithm that has one but not the other.
2. Why is naive `fib` exponential when there are only n distinct answers in it?
3. State the four questions, in order, from memory.
4. What's the difference between `dp[i]` = "best in the first i" and `dp[i]` =
   "best ending at i"? Which one makes the answer `max(dp)` rather than `dp[-1]`?
5. Give the formula for the time complexity of any DP.
6. When would you choose top-down memoization over bottom-up tabulation, and
   when the reverse?
7. What tells you a recurrence can drop to rolling variables, and how many
   variables do you need?
8. Why does tracking only the running maximum fail on a product?
9. Why is expand-around-centre still "the same idea" as a palindrome DP table?
10. What is pseudo-polynomial time, and which pattern here has it?
11. Give the trigger phrase for each of patterns A–I.

---

## 11. Ready?

**First timed problem: Climbing Stairs** (Easy) — Pattern A.

Say `timed climbing-stairs` and I'll give you the statement and start the
30-minute box. No hints while it's running.

**What "good" looks like here.** This is the easiest DP you will ever see, so
finishing is not the bar — the bar is running the *procedure* on a problem
simple enough that you can't hide:

- You write the four answers down **before** any code: state, recurrence, base
  cases, fill order
- You state the brute force and its complexity first, and name the overlapping
  subproblem out loud
- You get there in three explicit steps — naive recursion, then memo, then
  bottom-up — rather than jumping to the answer you half-remember
- You then space-optimise to O(1) unprompted, and say why the recurrence permits it
- You check n = 1 and n = 2 against the statement before declaring done

If you finish well inside 30 minutes, don't stop the clock — roll into
**House Robber** (Pattern B) and use the rest of the box.

**Expect not to finish some of these.** This is the topic where a 30-minute box
routinely ends with a half-written recurrence, and that is *data*, not failure.
It goes into the [[../meta/review-queue|review queue]] and comes back at +2 days.
Ten problems, repeated until cold-solvable, beats ten problems seen once —
especially here, where "I've seen it" and "I can derive it" are further apart
than anywhere else in the curriculum.
