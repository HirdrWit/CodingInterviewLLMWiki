---
type: lesson
topic: math-geometry
status: ready
updated: 2026-09-14
sources: [https://docs.python.org/3/reference/expressions.html#binary-arithmetic-operations, https://docs.python.org/3/library/functions.html#divmod, https://www.geeksforgeeks.org/inplace-rotate-square-matrix-by-90-degrees/, https://www.geeksforgeeks.org/print-a-given-matrix-in-spiral-form/]
tags: [lesson, math-geometry]
---

# Lesson 17 — Math & Geometry

**Curriculum:** [[../curriculum/math-geometry|Topic 17]] · **Materials:** [[../meta/resources|Resources]]
**Builds on:** [[../lessons/01-arrays-hashing|Lesson 1]] (arrays as contiguous
memory) and [[../curriculum/two-pointers|Topic 2]] (indices moving under a rule)
**Work through in:** 45–60 minutes. Three Mediums, no new data structure, no new
asymptotic idea. The difficulty lives somewhere else entirely — see §1.

---

## 1. Why this topic

Be honest about what this is: **there is no deep algorithmic idea here.**

Every problem in this topic can be described completely in one sentence to a
non-programmer. Rotate the picture. Read the grid in a spiral. Blank out the row
and column of every zero. Nobody gets stuck because they can't think of the
approach. There is no "aha".

So why do interviewers keep asking them? Because **they separate people who can
hold an index invariant in their head under pressure from people who can't.**
Every other topic lets you hide sloppy index work behind a clever insight — get
the insight, and the code mostly writes itself. Here there is no insight to hide
behind. What's left on the whiteboard is pure bookkeeping: off-by-ones, stale
values you overwrote before you read them, a loop that emits the middle row
twice, a "swap" that's actually two assignments of the same value.

That's the real reason this topic exists in the list. It's a **precision drill**.
The skill it tests is the one that makes your *other* solutions correct on the
first run — and being right first time, out loud, without a debugger, is most of
what an interviewer is actually grading.

There is a second payoff. Topic 1 taught the trade *spend memory, buy time*.
This topic teaches you to run that trade backwards: when the problem says
**O(1) extra space**, you can't allocate, so you have to **store your state
inside the input itself**. That move — the input doubles as scratch space —
shows up again in bit manipulation, in array-marking tricks, and in a fair
number of Hards. Here it's at its most visible.

---

## 2. The mental model

### A matrix is a list of rows, not a grid

The picture in your head is a grid. The thing in memory is a **list whose
elements are lists**. That mismatch is where half the bugs come from.

```
matrix = [ [1, 2, 3],      row 0 →  ┌───┬───┬───┐
           [4, 5, 6],               │ 1 │ 2 │ 3 │
           [7, 8, 9] ]              ├───┼───┼───┤
                          row 1 →   │ 4 │ 5 │ 6 │
                                    ├───┼───┼───┤
                          row 2 →   │ 7 │ 8 │ 9 │
                                    └───┴───┴───┘
                                      c0  c1  c2
```

**`matrix[r][c]` — row first, column second. Always.** Say it out loud until
it's reflex: *first index picks the row, second index walks along it.*

```
matrix[1][2]  →  matrix[1] is [4,5,6]  →  [2] of that is 6
```

Two consequences you will use constantly:

- `len(matrix)` is the number of **rows**. `len(matrix[0])` is the number of
  **columns**. Write them as `rows, cols = len(matrix), len(matrix[0])` at the
  top of every matrix problem, before you do anything else. Half of all index
  bugs are `rows` and `cols` swapped on a non-square input.
- Walking a **row** is cheap and natural (`matrix[r]` is one contiguous list).
  Walking a **column** means touching n different lists. Same O(n), but the
  asymmetry is why "reverse each row" is one line and "reverse each column"
  isn't.

### The physical analogy: it's a photograph

Hold a square photo. To rotate it 90° clockwise you can spin it — or you can do
two flips: **flip it along its main diagonal** (top-left to bottom-right), then
**flip it left-to-right**. Try it with a piece of paper with a letter F drawn on
it. The two-flip version is what a computer can do in place, because each flip
is just a pile of pairwise swaps and a swap needs no extra storage.

```
   original          transpose (flip on ↘ diagonal)     reverse each row
  ┌───┬───┬───┐         ┌───┬───┬───┐                  ┌───┬───┬───┐
  │ 1 │ 2 │ 3 │         │ 1 │ 4 │ 7 │                  │ 7 │ 4 │ 1 │
  ├───┼───┼───┤         ├───┼───┼───┤                  ├───┼───┼───┤
  │ 4 │ 5 │ 6 │   ──►   │ 2 │ 5 │ 8 │       ──►        │ 8 │ 5 │ 2 │
  ├───┼───┼───┤         ├───┼───┼───┤                  ├───┼───┼───┤
  │ 7 │ 8 │ 9 │         │ 3 │ 6 │ 9 │                  │ 9 │ 6 │ 3 │
  └───┴───┴───┘         └───┴───┴───┘                  └───┴───┴───┘
                                                       = rotated 90° CW
```

That's the whole of problem 1, and it's the model to carry: **a hard rearrangement
is often two easy rearrangements composed.** Ask "can I get there in two flips?"
before you try to compute a destination index directly.

---

## 3. The mechanics

### Transpose: swap across the diagonal, upper triangle only

Transposing means `matrix[i][j]` and `matrix[j][i]` trade places for every pair.
In Python the swap itself is one line, no temp variable:

```python
matrix[i][j], matrix[j][i] = matrix[j][i], matrix[i][j]
```

The right-hand side is fully evaluated into a tuple *before* anything is
assigned, so there's no clobbering. That's a real Python guarantee, not luck —
and worth saying aloud in an interview, because in C or Java you'd need a temp.

The part that actually matters is **which pairs you visit**:

```python
for i in range(n):
    for j in range(i + 1, n):      #  j > i  — upper triangle ONLY
        matrix[i][j], matrix[j][i] = matrix[j][i], matrix[i][j]
```

**Why `range(i + 1, n)` and not `range(n)`?** Because a swap is its own inverse.
Visiting cell `(0,2)` swaps it with `(2,0)`. If the loop later visits `(2,0)`, it
swaps them straight back, and the matrix ends up unchanged. Looping over all
`j` does every swap exactly twice, i.e. does nothing at all.

```
  cells with j > i  (the ones to swap)      each pair touched ONCE
  ┌───┬───┬───┐
  │ · │ ✓ │ ✓ │      · = diagonal, i == j, swaps with itself: skip
  ├───┼───┼───┤      ✓ = upper triangle, the loop body runs here
  │   │ · │ ✓ │      blank = lower triangle, already handled by its partner
  ├───┼───┼───┤
  │   │   │ · │
  └───┴───┴───┘
```

Starting at `j = i + 1` also skips the diagonal for free — `matrix[i][i]` swapping
with itself is harmless but pointless.

**Sanity check to run in your head:** after the loop, `matrix[0]` should be the
old first *column*. If it isn't, your bound is wrong.

### Boundary variables: four walls closing in

The spiral traversal has no clever trick either. You keep four numbers that name
the walls of the not-yet-visited rectangle, walk one wall, then move that wall
inward.

```
  top    ──►  ┌───────────────┐  ─┐
              │ → → → → → → → │   │
  left  │     │ ↑           ↓ │   │  right
        │     │ ↑           ↓ │   │
        ▼     │ ← ← ← ← ← ← ← │   │
  bottom ──►  └───────────────┘  ─┘
```

```python
top, bottom = 0, rows - 1
left, right = 0, cols - 1
while top <= bottom and left <= right:
    # 1. walk left→right along row `top`,    then top    += 1
    # 2. walk top→bottom down column `right`, then right -= 1
    # 3. walk right→left along row `bottom`,  then bottom -= 1
    # 4. walk bottom→top up column `left`,    then left   += 1
```

Two details are where people lose the problem:

**1. The loop condition is `and`, not `or`, and it's `<=`, not `<`.** `<=` because
a single remaining row (`top == bottom`) still has cells to emit. `and` because
the rectangle is empty as soon as *either* dimension collapses.

**2. Steps 3 and 4 need a guard.** After steps 1 and 2 the walls may have already
crossed, and walking back along a row that no longer exists emits cells twice.
Picture a 1×4 input after step 1 has consumed the only row: `top` is now 1,
`bottom` is still 0. Step 3 would walk that same row backwards.

```python
    if top <= bottom:      # before the right→left pass
        ...
    if left <= right:      # before the bottom→top pass
        ...
```

The failing cases are always a single leftover row or a single leftover column —
odd dimensions, or a 1×n / n×1 input. **Test a 3×3 and a 1×4 by hand before you
say you're done.** That is the entire difficulty of this problem.

### Encoding state in the input

When a problem demands O(1) extra space, you have no place to record "this row
needs clearing" — except the matrix itself. The move is to pick cells whose
*original* values you will no longer need once you're in the writing phase, and
overwrite them with flags.

The shape is always two phases, and **they must not interleave**:

```
  PASS 1 — read the whole grid, record findings into the chosen marker cells
  PASS 2 — read the markers, apply the changes
```

Interleaving is the bug that eats this problem: if you clear a row the instant
you find a zero, the zeros you just wrote are indistinguishable from zeros that
were in the input, and pass 2 clears everything. Whenever you store state inside
the data, ask: **can I still tell a flag apart from real data?** If not, you need
a separate marker channel — an extra variable or a distinguishing sentinel value.
One extra boolean is still O(1) space; a whole extra row is not.

### The small math that recurs

None of this is hard, but every one of these has cost someone a problem.

**Integer division and modulo.** `//` floors (rounds toward −∞), `%` takes the
sign of the *right* operand:

```python
 7 //  2   #  3        7 %  2   #  1
-7 //  2   # -4   ← not -3!     -7 %  2   #  1   ← not -1!
 7 // -2   # -4                  7 % -2   # -1
```

C, Java and C++ truncate toward zero instead, so `-7 / 2` is `-3` and `-7 % 2` is
`-1` there. If an interviewer asks you to port a solution, this is the first
thing that breaks. The invariant that always holds in Python is
`(a // b) * b + (a % b) == a`.

**`divmod`.** Gets both at once, and is the idiomatic way to turn a flat index
into `(row, col)`:

```python
r, c = divmod(index, cols)      # index 7 in a 3-column grid → (2, 1)
index = r * cols + c            # and back again
```

Knowing this both ways is worth having ready — flattening a 2-D grid to 1-D is a
standard follow-up ("can you do it with a single loop?").

**Overflow.** Python's ints are arbitrary-precision: they grow, they never wrap.
So overflow is a non-issue for you — but it is a favourite follow-up question.
The answer you want ready: *in Java or C++, a 32-bit `int` wraps silently past
±2³¹, so I'd use a 64-bit `long` for intermediate products, check the bound
before multiplying, or reformulate to avoid the large intermediate — for example
comparing `a > limit / b` instead of `a * b > limit`.* Having that sentence
ready costs nothing and reads as production experience.

---

## 4. Complexity

Let the matrix be `m × n`, and `n × n` where it must be square.

| Operation | Time | Space | Why |
|---|---|---|---|
| Touch every cell once | O(m·n) | O(1) | the floor for anything that reads the whole grid |
| Transpose in place | O(n²) | **O(1)** | ~n²/2 swaps, no allocation |
| Reverse one row | O(n) | O(1) | `row.reverse()` or two pointers |
| Reverse every row | O(n²) | O(1) | n rows × O(n) |
| Rotate = transpose + reverse rows | O(n²) | **O(1)** | two O(n²) passes compose |
| Spiral traversal | O(m·n) | O(1) extra | each cell emitted exactly once; the output list isn't counted |
| Marker pass + apply pass | O(m·n) | **O(1)** | two full sweeps, constant scratch |
| Build a rotated copy | O(n²) | **O(n²)** ✗ | correct, but fails the constraint |

Two things to read off this table:

- **Every row is O(1) space except the last one.** That's the point of the topic:
  the time complexity is fixed at "look at everything", so the only thing being
  graded is space.
- **O(n²) time is not a failure here.** There are n² cells; you cannot do better
  than reading them. Don't waste box time hunting for a faster algorithm that
  doesn't exist — spend it on getting the indices right.

---

## 5. The patterns

Each one: **the trigger in the problem statement → the tool it should summon.**
The trigger is the transferable half.

### Pattern A — compose two simple flips → [[../concepts/matrix-in-place-transform|matrix in-place transform]]
> **Trigger:** "rotate the matrix", "in-place", "do not allocate another matrix",
> and the input is guaranteed **square**

Don't try to derive the destination index of each cell in one go — that path
leads to the four-way cyclic ring swap, which is correct but very easy to get
wrong at a whiteboard. Instead ask: **is the target reachable as a composition of
a transpose and a reflection?** For the four rotations of a square, it always is:

| Want | Do this |
|---|---|
| 90° clockwise | transpose, then reverse each **row** |
| 90° counter-clockwise | transpose, then reverse each **column** (i.e. reverse the row *order*) |
| 180° | reverse row order, then reverse each row |

The tell that this pattern applies is the word **square**. Transpose in place
requires it — a non-square transpose changes the shape of the object, so it
cannot be done by swapping within the existing lists.

### Pattern B — four shrinking boundaries → [[../concepts/layer-by-layer-traversal|layer-by-layer traversal]]
> **Trigger:** "spiral", "in clockwise order", "layer by layer", "ring by ring",
> "peel the outer border"

Stop thinking about the path as one continuous motion and start thinking about
it as four straight walks repeated. Keep `top`, `bottom`, `left`, `right`; after
each walk, retire that wall. Loop while the rectangle is non-empty.

The alternative framing — "visit cells, mark them visited, turn right when you'd
hit something visited" — also works and costs O(m·n) space for the visited grid.
Mention it as the brute force, then do it with boundaries. The contrast is
exactly the kind of thing an interviewer is listening for.

### Pattern C — store the flags in the data → [[../concepts/in-place-marker|in-place marker]]
> **Trigger:** "modify in place", "**O(1) extra space**", "do not use additional
> memory for another array"

The obvious solution allocates a set of rows and a set of columns to clear: O(m+n)
space. When the follow-up removes that budget, the question becomes *where can I
put m+n bits that I'm allowed to destroy?* Answer: somewhere in the matrix whose
values the algorithm no longer needs — the **first row and first column** are the
natural choice, because they are exactly m+n cells and each one already "belongs"
to the row and column it would flag.

The catch, and the whole reason this is a Medium: the two marker lines **overlap
at cell (0,0)**, which would have to flag both its row and its column. One cell
cannot hold two independent bits, so one of them gets its own ordinary variable.
And because the marker cells sit *inside* the data, the order of operations
matters: read everything before you write anything, and handle the marker lines
themselves **last**, after they have finished being markers.

---

## 6. Python notes

```python
rows, cols = len(matrix), len(matrix[0])     # first line of every matrix problem

matrix[i][j], matrix[j][i] = matrix[j][i], matrix[i][j]   # swap, no temp

row.reverse()          # in place, O(n), returns None
row[::-1]              # a NEW reversed list — O(n) space. Not in-place!
matrix.reverse()       # reverses the ORDER OF ROWS, not the contents

for row in matrix:     # iterating rows directly is cleaner than range(len(...))
    row.reverse()

r, c = divmod(k, cols)          # flat index → (row, col)
list(zip(*matrix))              # transpose — but builds tuples, O(n²) space
```

`zip(*matrix)` is a lovely one-liner and you should know it exists. **Do not use
it when the problem says in-place** — it allocates a whole new structure, and
reaching for it signals you didn't notice the constraint. Know it, then don't
use it here.

**The aliasing trap, in full, because it bites hard:**

```python
grid = [[0] * 3] * 3       # WRONG — three references to the SAME row
grid[0][0] = 1
# grid is now [[1,0,0], [1,0,0], [1,0,0]]

grid = [[0] * 3 for _ in range(3)]   # RIGHT — three independent rows

copy = matrix[:]           # shallow: new outer list, SAME row objects
copy[0][0] = 99            # mutates the original too
copy = [row[:] for row in matrix]    # deep enough for a matrix of numbers
```

---

## 7. Traps

- **Looping `j` over all of `range(n)` in the transpose.** Every pair swaps
  twice, the matrix comes out unchanged, and because it *looks* right you'll
  stare at it for five minutes. `range(i + 1, n)`.
- **Missing the guards in the spiral.** Passes back along the bottom row and up
  the left column must be skipped when the rectangle has collapsed to a single
  row or column. Symptom: duplicated values in the middle of the output. Test a
  3×3 and a 1×4.
- **`rows` and `cols` swapped.** Invisible on square test cases, instant
  `IndexError` on a 2×5. Always try a non-square input if the problem allows one.
- **Interleaving the marking pass and the applying pass.** Zeros you wrote become
  indistinguishable from zeros that were given, and the whole matrix goes to
  zero. Two separate passes, always.
- **Aliasing with `[[0] * n] * n`.** One row, referenced n times. Writing to one
  writes to all.
- **Modifying a list while iterating it.** Removing or inserting during
  `for x in row` silently skips elements. Build a new list, or iterate over a
  copy, or iterate by index deliberately.
- **`row[::-1]` when you meant `row.reverse()`.** The slice allocates a new list
  and returns it; assigning it to a loop variable changes nothing in the matrix,
  and your "in-place" claim was never true.
- **Claiming O(1) space while allocating.** A new output matrix, a `visited`
  grid, `list(zip(*matrix))` — all O(m·n). Say the space complexity out loud
  *and* point at the line that justifies it. Getting caught overstating this is
  worse than having written the O(n) version honestly.
- **Not saying which rotation direction you implemented.** Clockwise and
  counter-clockwise differ by one line. State the direction, then show it on a
  2×2 by hand.

---

## 8. Worked example

Nothing from the problem set, so nothing is spoiled.

> **Problem.** *Reshape the Matrix.* Given an `m × n` matrix and target
> dimensions `r × c`, return a new `r × c` matrix holding the same elements in
> the same row-major order. If the reshape is impossible, return the original.
>
> `[[1,2],[3,4]]`, `r=1`, `c=4` → `[[1,2,3,4]]`

**First, the guard.** A reshape is possible exactly when `m * n == r * c`. Say
this before writing anything — it's the only edge case, and forgetting it is the
whole test.

**Brute force.** Flatten into a list, then chop it into `r` slices of length `c`.
O(m·n) time and O(m·n) extra space for the flat list. Perfectly acceptable, and
worth stating.

**Spot the pattern.** The flat list is only ever used as an *addressing scheme* —
element `k` of it is `matrix[k // n][k % n]`. So we don't need the list; we need
the arithmetic. That's `divmod` from §3, used in both directions at once.

```python
def reshape(matrix, r, c):
    rows, cols = len(matrix), len(matrix[0])
    if rows * cols != r * c:
        return matrix

    result = [[0] * c for _ in range(r)]      # independent rows — see §6
    for k in range(r * c):
        src_r, src_c = divmod(k, cols)        # k as a position in the OLD shape
        dst_r, dst_c = divmod(k, c)           # k as a position in the NEW shape
        result[dst_r][dst_c] = matrix[src_r][src_c]
    return result
```

**Complexity.** O(m·n) time. O(1) *extra* space — the output doesn't count, and
there is no intermediate flat list.

**The move to internalise:** a flat index `k` and a coordinate pair `(r, c)` are
the same information in two costumes, and `divmod` is the change of costume.
Once you see that, "walk this grid in some order" problems stop needing a
scratch array to walk through. The same trick converts most nested double loops
into a single loop when an interviewer asks for one.

---

## 9. Materials

Full list on [[../meta/resources|Resources]]. For this lesson, in order:

1. [In-place rotate a square matrix by 90°](https://www.geeksforgeeks.org/inplace-rotate-square-matrix-by-90-degrees/)
   — 10 min. Read it for the two-flip decomposition, not the code.
2. [Print a matrix in spiral form](https://www.geeksforgeeks.org/print-a-given-matrix-in-spiral-form/)
   — 10 min. The four-boundary version; note where the guards go.
3. [Python binary arithmetic operations](https://docs.python.org/3/reference/expressions.html#binary-arithmetic-operations)
   — skim for the exact definition of `//` and `%` with negatives.
4. [`divmod` in the Python docs](https://docs.python.org/3/library/functions.html#divmod)
   — 2 min, and then it's yours forever.
5. [NeetCode on YouTube](https://www.youtube.com/@NeetCode) — search the problem
   name, but **only after** a timed attempt and a debrief.

A pencil and squared paper beat all five of these for this particular topic.
Draw the 3×3 and step the indices by hand.

---

## 10. Self-check

Answer these **aloud**, in full sentences, before starting the timer.

1. In `matrix[a][b]`, which index is the row? How do you get the number of rows
   and the number of columns?
2. Why does the transpose loop use `range(i + 1, n)` for `j`? What exactly goes
   wrong with `range(n)`?
3. Give the two-step recipe for a 90° clockwise rotation. Now the
   counter-clockwise one.
4. Why does in-place transpose require a square matrix?
5. Name the four boundary variables in a spiral traversal and say when each one
   moves. Why is the loop condition `and` rather than `or`?
6. What input breaks a spiral traversal that's missing its guards, and what does
   the wrong output look like?
7. What does "O(1) extra space" force you to do, and where do the flags go?
8. Why must the marking pass and the applying pass be separate?
9. What is `-7 // 2` in Python? What is `-7 % 2`? What would C give you?
10. What's wrong with `[[0] * n] * n`?
11. Give the trigger phrase for each of patterns A, B and C.

---

## 11. Ready?

**First timed problem: Rotate Image** (Medium) — Pattern A.

Say `timed rotate-image` and I'll give you the statement and start the
30-minute box. No hints while it's running.

**What "good" looks like here.** This topic is graded on precision, so the bar
is different from the pattern-recognition topics:

- You write `rows, cols = len(matrix), len(matrix[0])` before anything else, out
  of habit
- You **draw the 3×3 and step it by hand** before writing the loop — on this
  topic, five minutes of paper is faster than five minutes of debugging
- You state the O(n²)-space copy version first, then say why the constraint rules
  it out
- You get the `j > i` bound right the first time, and can say *why* without
  being asked
- You verify on a 2×2 by hand, not by running it
- You state time **and** space, and point at the line that makes the space claim
  true

If you finish inside 30 minutes, don't stop the clock — roll into **Spiral
Matrix** and spend the remaining time on the guards. That's the one that punishes
hand-waving.

An unfinished Medium here is fine and expected; it goes in the
[[../meta/review-queue|review queue]] at +2 days. But note the specific failure
mode to watch for in the debrief: on this topic, "I had the right idea and the
code was wrong" is not a near-miss — **it is the exact thing the topic is
testing.** Treat an index bug here as seriously as a wrong approach elsewhere.
