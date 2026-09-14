---
type: lesson
topic: 2d-dynamic-programming
status: ready
updated: 2026-09-14
sources: [https://www.geeksforgeeks.org/dynamic-programming/, https://www.geeksforgeeks.org/tabulation-vs-memoization/, https://docs.python.org/3/library/functools.html]
tags: [lesson, 2d-dynamic-programming, dynamic-programming]
---

# Lesson 14 — 2-D Dynamic Programming

**Curriculum:** [[../curriculum/2d-dynamic-programming|Topic 14]] · **Materials:** [[../meta/resources|Resources]]
**Prerequisites:** [[13-1d-dynamic-programming|Lesson 13 — 1-D DP]]. Everything here assumes
you already know what a state, a recurrence, and a base case are.
**Work through in:** 60–90 minutes. Only two problems in this topic, but the
table shape in section 3 is the most reusable artefact in the whole DP track.

---

## 1. Why this topic

The obvious reason: there are two Blind 75 problems here and you have to do them.

The real reason: **1-D DP taught you the machinery, but it let you cheat on the
hardest part.** In Topic 13 the state was handed to you by the problem —
"the answer for the first `i` houses", "the answer for amount `n`". One number
in, one number out. You never had to *design* a state; you read it off the
problem statement.

2-D DP is where state design becomes a real decision. The question stops being
"what's the recurrence" and becomes **"what do I need to know about my position
to compute the rest?"** — and sometimes the honest answer needs two numbers, not
one. That shift is the whole topic. Once you can ask that question, edit
distance, knapsack, interleaving strings, palindrome partitioning and regex
matching all stop being separate problems and become the same problem with
different cell contents.

Two Blind 75 problems, one genuinely transferable skill. Worth the time.

---

## 2. The mental model

### One dimension is a corridor. Two is a floor plan.

In 1-D DP you walked a hallway, and each door's answer depended on one or two
doors behind you:

```
dp:  [ 0 ][ 1 ][ 2 ][ 3 ][ 4 ]
              ↖   ↖
              only what's behind you on the same line
```

In 2-D DP you're standing on a floor, and a cell's answer depends on cells
*above*, *to the left*, and *diagonally back*:

```
        j-1    j
      ┌──────┬──────┐
 i-1  │  ↖   │  ↑   │
      ├──────┼──────┤
  i   │  ←   │ dp   │      dp[i][j] is computed from at most
      └──────┴──────┘      dp[i-1][j-1], dp[i-1][j], dp[i][j-1]
```

Every 2-D DP problem in interviews is one of two stories about why there are two
axes:

**Story 1 — the axes are literally space.** You are on a grid and `(i, j)` is a
square. Movement is restricted (usually down and right only), which is what makes
the whole thing a DAG and therefore solvable by DP at all. See
[[../concepts/dp-grid]].

**Story 2 — the axes are two sequences being compared.** `i` is how far you've
consumed string A, `j` is how far you've consumed string B. There is no physical
grid; the "grid" is the space of *pairs of prefixes*. See
[[../concepts/subsequence-dp]].

Story 2 is the one that feels like a trick the first three times and then never
feels like a trick again. It is also the one interviewers ask about far more
often, so it gets the careful treatment below.

---

## 3. The mechanics

### 3a. The grid case

Nothing subtle here, and that's the point — do this one first to get your hands
warm. The cell `dp[i][j]` holds *the answer for the subproblem ending at square
`(i, j)`*: number of ways to reach it, cheapest cost to reach it, biggest square
with its corner there, whatever the problem asks.

A cell that can only be entered from above or from the left has a recurrence
built out of exactly those two neighbours, and the base cases are the top row and
the left column — the cells with a missing neighbour. Fill top-to-bottom,
left-to-right, and every neighbour you need is already computed when you get
there. That's the whole method.

The real work in a grid problem is not the recurrence, it's **reading the
movement rules out of the statement** and noticing which edges of the grid are
special.

### 3b. The two-sequence table — the shape to learn cold

Here is the shape. Memorise the shape, not any one problem's recurrence.

> `dp[i][j]` = the answer for **the first `i` characters of A** and
> **the first `j` characters of B**.

Read that twice. `i` and `j` are *counts*, not indices. `dp[2][3]` is about
`A[:2]` and `B[:3]`. That single decision is what buys you clean base cases, and
it is why the table is `(m+1) × (n+1)` rather than `m × n`.

**Why the extra row and column exist.** Row 0 means "A contributes nothing" and
column 0 means "B contributes nothing" — the empty prefix. Every one of these
problems has a trivially known answer when one side is empty (the LCS of anything
with `""` is 0; the edit distance from `""` to a string of length `j` is `j`), so
row 0 and column 0 are your base cases, free, no special-casing inside the loop.
Without the offset you'd be writing `if i == 0` guards in the hot path and
getting them wrong.

**The cost of the offset — and this is where people bleed time:** the character
that cell `dp[i][j]` is *about* is `A[i-1]` and `B[j-1]`, not `A[i]` and `B[j]`.
Write that down. Say it out loud when you set up the table. Almost every bug in
this pattern is that one subtraction.

**The match / no-match branch.** With `i` and `j` fixed, you're deciding what to
do with the last character of each prefix. There are exactly two worlds:

- **They match** (`A[i-1] == B[j-1]`). The pair can be consumed together, and the
  rest of the answer is whatever was true for `A[:i-1]` and `B[:j-1]` — that's
  the **diagonal** cell `dp[i-1][j-1]`, usually plus one for the pair you just
  used. *Match ⇒ look diagonally back.* It's the only move that consumes from
  both strings at once.
- **They don't match.** You must give up on one side's last character. Dropping
  A's leaves `dp[i-1][j]` (**above**); dropping B's leaves `dp[i][j-1]`
  (**left**). Take whichever the problem wants — `max` when you're building
  something up, `min` when you're paying a cost.

So: **diagonal is the "they agree" move, up and left are the "sacrifice one"
moves.** Every variant of this family differs only in what goes in the cell and
what the no-match branch costs. Get that sentence solid and the family collapses
into one thing.

### 3c. A filled-in table, cell by cell

Worked on **longest common *substring*** — contiguous, so it is deliberately
*not* the Blind 75 problem, but the table shape is identical.

Here `dp[i][j]` = *length of the longest common suffix of `A[:i]` and `B[:j]`*,
i.e. a run that ends exactly at both of those positions. Match extends the run
by one from the diagonal; a mismatch kills the run dead.

```
A = "ABCB"   (rows)        B = "BDCB"   (columns)

              ""    B     D     C     B
            ┌─────┬─────┬─────┬─────┬─────┐
       ""   │  0  │  0  │  0  │  0  │  0  │   ← row 0: A empty
            ├─────┼─────┼─────┼─────┼─────┤
       A    │  0  │  0  │  0  │  0  │  0  │
            ├─────┼─────┼─────┼─────┼─────┤
       B    │  0  │  1  │  0  │  0  │  1  │
            ├─────┼─────┼─────┼─────┼─────┤
       C    │  0  │  0  │  0  │  1  │  0  │
            ├─────┼─────┼─────┼─────┼─────┤
       B    │  0  │  1  │  0  │  0  │ [2] │
            └─────┴─────┴─────┴─────┴─────┘
              ↑
              column 0: B empty
```

Walk it:

- **Row "A" (i=1).** `A[0]` is `A`. None of `B`, `D`, `C`, `B` is an `A`, so
  every cell is a mismatch and the run length is 0.
- **Row "B" (i=2), column "B" (j=1).** `A[1] == B[0] == 'B'`. Match: take the
  diagonal `dp[1][0]`, which is 0, add 1 → **1**. A one-character run `"B"`.
- **Row "B", column "D" (j=2).** `'B' != 'D'` → 0. Not "carry the 1 across" —
  a substring must be contiguous, so a mismatch resets.
- **Row "B", column "B" (j=4).** Match again. Diagonal is `dp[1][3]` = 0, so
  **1**. Another fresh one-character run.
- **Row "C" (i=3), column "C" (j=3).** `'C' == 'C'`. Diagonal `dp[2][2]` = 0
  (that was the `B`/`D` mismatch), so **1**.
- **Row "B" (i=4), column "B" (j=4).** `'B' == 'B'`. Diagonal is `dp[3][3]` = 1
  — the `C`/`C` cell. So **1 + 1 = 2**: the run `"CB"`, which is indeed the
  longest common substring of `ABCB` and `BDCB`.

Notice the **2** was built by a diagonal chain: the `C` cell fed the `B` cell.
That diagonal chain is the answer, drawn in the table. Whenever you're stuck on
one of these, hand-fill a 4×4 example. Four minutes of arithmetic beats twenty
minutes of staring at a recurrence.

Also notice what "where's the answer?" means here: for this contiguous variant it
is the **maximum over the whole table**, because a run can end anywhere. For the
variants where the cell means "answer for the whole pair of prefixes" instead of
"answer ending here", the answer is the **bottom-right corner**. Deciding which
of those two meanings your cell has is part of designing the state, and getting
it wrong produces code that is right everywhere except the return statement.

### 3d. Fill order

The rule is mechanical: **fill in an order where every cell a recurrence reads is
already written.** If you read `[i-1][*]` and `[i][j-1]`, then rows
top-to-bottom, columns left-to-right works. If your recurrence reached *forward*
— `dp[i+1]`, `dp[i][j+1]` — you iterate backwards instead. Nothing deeper.

Memoised recursion (a dict or `functools.lru_cache` over `(i, j)`) sidesteps fill
order entirely: the call stack discovers a valid order for you. It is the faster
thing to write under time pressure and a perfectly good interview answer — say
"top-down memo first, and I can convert it to a bottom-up table if you want the
space savings." Just watch the recursion depth on large inputs.

### 3e. Rolling rows: O(m×n) → O(min(m,n))

Look again at the recurrence: `dp[i][j]` reads only from row `i-1` and from row
`i` to its left. **Row `i-2` is never touched again.** So there is no reason to
keep the whole table — two rows is enough:

```python
prev = [0] * (n + 1)
for i in range(1, m + 1):
    curr = [0] * (n + 1)
    for j in range(1, n + 1):
        curr[j] = ...          # reads prev[j-1], prev[j], curr[j-1]
    prev = curr
return prev[n]
```

Two rows of length `n+1` is O(n) space. And since you get to choose which string
is the rows, **make the longer string the rows and the shorter one the columns**
— that's the O(min(m, n)) in the headline. It's a one-line swap at the top and
it's the kind of detail that reads as fluency:

```python
if len(a) < len(b):
    a, b = b, a       # iterate over the long one, keep rows short
```

**Two costs to state honestly when you offer this.** First, you lose the table,
so you can no longer *reconstruct* the actual subsequence / alignment by walking
back through it — only the numeric answer survives. If the problem asks for the
string itself, keep the full table. Second, the single-array version (mutating
one row in place instead of keeping two) works only if you're careful about the
diagonal: `dp[j-1]` has already been overwritten with the current row by the time
you read it, so you must stash the old value first, or iterate `j` backwards.
That's the classic knapsack space trick, and it is a genuine source of
silent wrong answers. **Two explicit rows is the version to write under pressure.**

---

## 4. Complexity

For an `m × n` table — grid dimensions, or the two string lengths.

| Quantity | Cost | Why |
|---|---|---|
| Number of states | O(m × n) | every `(i, j)` pair is one subproblem |
| Work per state | **O(1)** | a comparison and a `max`/`min`/`+` over ≤3 neighbours |
| Time | **O(m × n)** | states × work-per-state. This is the DP formula, always. |
| Space, full table | O(m × n) | needed if you must reconstruct the answer |
| Space, rolled | **O(min(m, n))** | two rows; put the longer string on the rows |
| Brute-force alternative | O(2^(m+n)) | branch on "drop from A" / "drop from B" at every step |

The last row is what you say out loud first. The exponential brute force and the
polynomial DP differ *only* because the brute force recomputes the same
`(i, j)` pair over and over — the table is nothing but a place to write each
answer down once. Say that and you've explained DP in one sentence.

One more habit worth building: **states × work-per-state is the DP complexity
formula.** It still works when the inner step is a loop instead of O(1) — then
you get O(m × n × k), and noticing that inner loop is often how you spot that
your state is wrong.

---

## 5. The patterns

Each one: **the trigger in the statement → the tool.** The trigger is the half
that transfers.

### Pattern A — the grid table → [[../concepts/dp-grid|dp-grid]]
> **Trigger:** "an m × n grid", "robot / start at the top-left, finish at the
> bottom-right", "you may only move down or right", "minimum cost path",
> "number of distinct paths"

Two axes, one square per cell. The movement restriction is the load-bearing part
of the statement: *down-and-right-only* means you can never revisit a cell, which
is exactly what makes a plain table work. Base cases are the edges that have a
missing neighbour; fill top-to-bottom, left-to-right.

**The tell that it's DP and not BFS:** you're asked to *count* paths or *optimise
over all* paths, not to find one path. Counting and optimising accumulate; finding
searches.

**Counting and optimising are the same table with a different operator.** "How
many ways" sums the incoming neighbours; "cheapest way" takes the min of them and
adds the cell's own cost. Same skeleton, one character different.

### Pattern B — the two-sequence table → [[../concepts/subsequence-dp|subsequence-dp]]
> **Trigger:** **two strings or two arrays given as input**, and words like
> "common", "match", "interleave", "transform A into B", "subsequence",
> "distinct ways to form", "align"

Two inputs and a question about how they relate is the single loudest signal in
DSA. Almost nothing else produces that shape. Reach for the `(m+1) × (n+1)`
table, `dp[i][j]` over prefixes, and the match/no-match branch from section 3b.

Where the family goes, all on the same skeleton:
- **edit distance** — mismatch costs 1 and has a third option (the diagonal
  *with* a cost, i.e. substitute); worked in full in section 8
- **longest common substring** — mismatch resets to 0 instead of taking a max
- **regex / wildcard matching** — the branch splits further on `*` and `.`
- **distinct subsequences, interleaving strings** — same table, different cell meaning

If you can hand-fill the table in section 3c from memory, you have all of these.

### Pattern C — index × budget (the knapsack shape)
> **Trigger:** "choose a subset of these items", "subject to a total
> weight/capacity/sum of at most W", "can the array be partitioned into two
> equal halves"

Not in this topic's two problems, but it is the third reason a state needs two
dimensions and it comes up in real interviews constantly, so know that it exists.
The axes here are **which items you've considered** × **how much budget you have
left**. The branch is *take this item* (move the budget axis) versus *skip it*
(don't). Recognising it as the same `dp[i][j]` machinery is the point; the
details can wait until you meet one.

### The meta-pattern — how to know you need two dimensions

Ask: **"I'm partway through. What is the smallest set of numbers that tells me
everything I need to finish?"** If the honest answer needs two numbers — position
*and* remaining budget, progress in A *and* progress in B — your DP is 2-D. If
you find yourself in a 1-D solution passing extra context around in a variable
that isn't the loop index, that variable is your missing second dimension.

---

## 6. Python notes

```python
# Allocate the table — ONE correct way and one that will ruin your day:
dp = [[0] * (n + 1) for _ in range(m + 1)]   # correct: m+1 independent rows
dp = [[0] * (n + 1)] * (m + 1)               # BROKEN: m+1 refs to ONE row

# Top-down instead, when the table's fill order is fiddly:
from functools import lru_cache

@lru_cache(maxsize=None)
def best(i, j):
    if i == 0 or j == 0:
        return 0
    ...
# args must be hashable — pass indices, never slices or lists.
# Slicing inside a recursion is also O(k) per call and quietly kills the complexity.

for i in range(1, m + 1):          # 1-based: dp row i is about a[i-1]
    for j in range(1, n + 1):
        if a[i - 1] == b[j - 1]:   # the -1 that causes every bug in this topic
            ...
```

Recursion depth: CPython's default limit is 1000, and a memoised
`(i, j)` recursion can nest `m + n` deep. Fine for interview-sized inputs,
worth a sentence if the constraints are large — "I'd convert this to bottom-up to
avoid stack depth" is the right thing to say.

`math.comb(n, k)` exists and there is a closed-form combinatorial answer to some
grid-counting problems. Mentioning it after you've given the DP is a nice flex.
Leading with it dodges the question the interviewer actually asked.

---

## 7. Traps

- **`[[0] * n] * m`.** The outer `*` copies the *reference*, so all `m` rows are
  the same list — writing `dp[0][0]` writes every row's first cell. Your table
  fills with garbage and nothing looks wrong. Use
  `[[0] * n for _ in range(m)]`. Always. This is the single most expensive
  five-character mistake in DP and it is completely silent.
- **`A[i]` where you meant `A[i-1]`.** With the +1 offset, the row index and the
  character index differ by one, permanently. Write the meaning of `dp[i][j]` as
  a comment above the loop before you write the loop.
- **Sizing the table `m × n` instead of `(m+1) × (n+1)`.** Then there's no empty
  prefix, so base cases move inside the loop as `if` guards, and you'll get one
  of them wrong. Pay the extra row and column.
- **Returning the wrong cell.** Bottom-right if the cell means "answer for both
  full prefixes"; max over the whole table if the cell means "answer *ending
  here*". Decide which one your cell means when you define it, not at the end.
- **Filling in an order that reads unwritten cells.** If a recurrence needs
  `dp[i][j+1]`, a left-to-right inner loop reads a cell from the wrong iteration
  — and it reads a plausible-looking zero rather than crashing.
- **Rolling to one row without handling the diagonal.** `dp[j-1]` is already the
  *current* row by the time you read it. Stash the old value or iterate
  backwards. Two explicit rows is safer under time pressure.
- **Claiming O(n) space after rolling without saying which n.** It's
  O(min(m, n)) only if you put the longer string on the rows. Say the whole thing.
- **Not stating the brute force first.** The exponential version and *why* it
  repeats work is the setup that makes the table obviously correct. Skipping it
  makes a correct solution look memorised.
- **Empty-input edge cases.** One or both strings empty, a 1×n grid, a 1×1 grid.
  The +1 row and column handle these for free — which is exactly why you should
  test them, to confirm you actually got the base cases right.

---

## 8. Worked example

Outside the problem set, so nothing is spoiled: **edit distance** (Levenshtein).
It is the canonical two-string table and a genuine interview favourite.

> **Problem.** Given words `a` and `b`, return the minimum number of single-
> character operations — insert, delete, or replace — to turn `a` into `b`.
> `"horse"` → `"ros"` is 3.

**Brute force.** At each step, try inserting, try deleting, try replacing,
recurse. Three-way branching to depth `m + n` → exponential, and it re-solves the
same `(i, j)` pair constantly. Say this out loud first, every time.

**Spot the pattern.** Two strings in, one relationship question out — **Pattern
B**. So: `dp[i][j]` = the minimum edits to turn `a[:i]` into `b[:j]`, on an
`(m+1) × (n+1)` table.

**Base cases, from the empty prefixes.** Turning `""` into `b[:j]` needs `j`
inserts, so row 0 is `0, 1, 2, …, n`. Turning `a[:i]` into `""` needs `i`
deletes, so column 0 is `0, 1, 2, …, m`. That whole paragraph is the payoff for
the +1 offset; without it these would be `if` statements in the loop.

**The branch.** Last characters `a[i-1]` and `b[j-1]`:

- **Match** → they need no work at all; the cost is whatever `dp[i-1][j-1]` was.
  Diagonal, no `+1`. (Compare with the substring table in section 3c, where the
  match branch *added* one. Same move, different cell contents.)
- **No match** → pay 1, then take the cheapest of the three repairs:
  `dp[i-1][j-1]` (replace), `dp[i][j-1]` (insert), `dp[i-1][j]` (delete).

```python
def edit_distance(a, b):
    m, n = len(a), len(b)
    dp = [[0] * (n + 1) for _ in range(m + 1)]      # NOT [[0]*(n+1)]*(m+1)

    for j in range(n + 1):
        dp[0][j] = j                                # "" → b[:j] costs j inserts
    for i in range(m + 1):
        dp[i][0] = i                                # a[:i] → "" costs i deletes

    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if a[i - 1] == b[j - 1]:                # the -1s: row i is about a[i-1]
                dp[i][j] = dp[i - 1][j - 1]
            else:
                dp[i][j] = 1 + min(dp[i - 1][j - 1],   # replace
                                   dp[i][j - 1],       # insert
                                   dp[i - 1][j])       # delete
    return dp[m][n]
```

**Complexity.** O(m × n) time, O(m × n) space — and then, unprompted: "the
recurrence only reads the previous row, so I can roll it down to two rows,
O(min(m, n)) space, at the cost of not being able to reconstruct the actual edit
sequence." Offering that trade before being asked is what separates a pass from
a strong pass.

**The move to internalise:** the table was not invented, it was *read off the
brute-force recursion*. The recursive arguments `(i, j)` became the axes; the
recursive base cases became row 0 and column 0; the three recursive calls became
three neighbouring cells. **Write the recursion, then the table is bookkeeping.**
If a 2-D problem ever stalls you in an interview, fall back to that order — it
always produces something, and the something is usually correct.

---

## 9. Materials

Full list on [[../meta/resources|Resources]]. In order:

1. Re-read section 5 of [[13-1d-dynamic-programming|Lesson 13]] before anything
   else — the state/recurrence/base-case vocabulary is assumed here.
2. [Dynamic programming hub](https://www.geeksforgeeks.org/dynamic-programming/)
   — GeeksforGeeks. Use the table-of-contents, not the whole page; you want the
   grid-path and string-DP sections only.
3. [Tabulation vs memoization](https://www.geeksforgeeks.org/tabulation-vs-memoization/)
   — 10 minutes, and it settles the top-down/bottom-up question for good.
4. [`functools.lru_cache`](https://docs.python.org/3/library/functools.html) —
   read the actual docs once so you know the hashable-arguments constraint.
5. [NeetCode on YouTube](https://www.youtube.com/@NeetCode) — search the problem
   name, but **only after** a timed attempt and a debrief.
6. Optional extra reps, when the table shape hasn't stuck: search LeetCode for
   *Minimum Path Sum*, *Longest Common Substring*, and *Edit Distance*. Three
   problems, one skeleton — that repetition is the fastest way to make this
   automatic. Study-plan hub: [LeetCode study plans](https://leetcode.com/studyplan/).

---

## 10. Self-check

Aloud, in full sentences, before the timer starts.

1. What does `dp[i][j]` mean in a two-string table — precisely, including whether
   `i` is an index or a count?
2. Why does the table have `m+1` rows and `n+1` columns? What lives in row 0?
3. Which character does row `i` correspond to, and why isn't it `A[i]`?
4. In the match branch, which neighbour do you read, and why that one?
5. In the no-match branch, what do the "above" and "left" cells each represent
   in terms of the original strings?
6. What determines the fill order? How would you know you'd chosen a wrong one?
7. Which rows of the table are still live when you're computing row `i`? What
   does that let you throw away, and what do you lose by throwing it away?
8. Why O(min(m, n)) rather than O(n) after rolling?
9. What is wrong with `dp = [[0] * n] * m`, and what does the bug look like?
10. Give the trigger phrases for patterns A, B and C.
11. State the brute-force complexity for a two-string problem and explain, in one
    sentence, why the table removes the exponent.

If 3, 4 or 9 come out mumbled, go back and hand-fill the table in section 3c on
paper. That exercise fixes all three.

---

## 11. Ready?

**First timed problem: Unique Paths** (Medium) — Pattern A.

Grid before strings, deliberately: it's the same machinery with the state design
already done for you, so you get a clean rep on table setup and fill order before
the harder half of the topic.

Say `timed unique-paths` and I'll give you the statement and start the 30-minute
box. No hints while it's running.

**What "good" looks like in the box:**

- You state the brute force — recurse from the start square, branching down and
  right — and name its complexity, *before* writing the table
- You say what `dp[i][j]` means in words before you allocate anything
- You allocate with a comprehension, not `* m`
- You identify the base-case edges yourself from the movement rules
- You state time *and* space complexity unprompted, then volunteer the
  row-rolling optimisation and what it costs you
- You check the degenerate inputs: a 1×1 grid, a 1×n grid

Finishing in well under 30 minutes is likely on this one. Don't stop the clock —
roll straight into **Longest Common Subsequence**, which is where the real
learning in this topic is. That one is worth a full box on its own; if it doesn't
finish, that's expected and it goes into the [[../meta/review-queue|review queue]]
at +2 days.

**Repeat until cold-solvable is the plan, not the fallback.** Two problems in
this topic means two chances to build a table shape you'll use for the rest of
your career — treat them accordingly.
