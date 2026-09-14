---
type: concept
status: unseen
updated: 2026-09-14
sources: [wiki/lessons/17-math-geometry.md]
tags: [math-geometry, matrix]
---

# Matrix In-Place Transform

**In one sentence:** A hard rearrangement of a square matrix is usually two easy
rearrangements composed — transpose it, then reverse each row — and both halves
are pure pairwise swaps, so neither needs a byte of extra storage.

Pattern A in [[../lessons/17-math-geometry|Lesson 17]].

## When to reach for it

Trigger: "rotate the matrix", "in place", "do not allocate another matrix", and
the input is guaranteed **square**.

The word **square** is the real tell. Transposing in place swaps `matrix[i][j]`
with `matrix[j][i]`, and that only stays inside the existing lists when the two
dimensions match. A non-square transpose changes the *shape* of the object, so
it cannot be done by swapping.

Generally: before deriving each cell's destination index directly, ask **"can I
get there in two flips?"** The direct route leads to a four-way cyclic ring swap
— correct, but very easy to get wrong at a whiteboard under time.

## How it works

Think of a photograph. To rotate it 90° clockwise you can spin it, or you can
flip it along its main diagonal and then flip it left-to-right. Same result, and
the second version is all the computer can do without a second sheet of paper.

```
   original          transpose (flip on ↘)          reverse each row
  ┌───┬───┬───┐         ┌───┬───┬───┐                ┌───┬───┬───┐
  │ 1 │ 2 │ 3 │         │ 1 │ 4 │ 7 │                │ 7 │ 4 │ 1 │
  ├───┼───┼───┤         ├───┼───┼───┤                ├───┼───┼───┤
  │ 4 │ 5 │ 6 │   ──►   │ 2 │ 5 │ 8 │      ──►       │ 8 │ 5 │ 2 │
  ├───┼───┼───┤         ├───┼───┼───┤                ├───┼───┼───┤
  │ 7 │ 8 │ 9 │         │ 3 │ 6 │ 9 │                │ 9 │ 6 │ 3 │
  └───┴───┴───┘         └───┴───┴───┘                └───┴───┴───┘
                                                     = rotated 90° CW
```

All four rotations come out of the same two ingredients, which is why this is
worth learning as a composition rather than as one memorised loop:

| Want | Do this |
|---|---|
| 90° clockwise | transpose, then reverse each **row** |
| 90° counter-clockwise | transpose, then reverse the **row order** |
| 180° | reverse the row order, then reverse each row |

## Minimal example

```python
n = len(matrix)

for i in range(n):
    for j in range(i + 1, n):      #  j > i — upper triangle ONLY
        matrix[i][j], matrix[j][i] = matrix[j][i], matrix[i][j]

for row in matrix:
    row.reverse()
```

**Why `range(i + 1, n)` and not `range(n)`?** This is the whole concept, and it
is the one thing to be able to say out loud.

A swap is its own inverse. Visiting cell `(0,2)` swaps it with `(2,0)`. If the
loop *later* visits `(2,0)`, it swaps the same pair straight back. Looping `j`
over all of `range(n)` performs every swap exactly twice, which is to say it
performs nothing — the matrix comes out unchanged, and because the code *looks*
right you will stare at it for five minutes.

```
  cells with j > i  (the ones to swap)      each pair touched ONCE
  ┌───┬───┬───┐
  │ · │ ✓ │ ✓ │      · = diagonal, i == j, swaps with itself: skip
  ├───┼───┼───┤      ✓ = upper triangle, the loop body runs here
  │   │ · │ ✓ │      blank = lower triangle, done by its partner already
  ├───┼───┼───┤
  │   │   │ · │
  └───┴───┴───┘
```

Starting at `j = i + 1` skips the diagonal for free as a bonus — `matrix[i][i]`
swapping with itself is harmless but pointless.

The Python swap needs no temp variable: the right-hand side is fully evaluated
into a tuple *before* anything is assigned, so there is no clobbering. That is a
language guarantee, not luck, and worth saying aloud — in C or Java you'd need a
temp.

## Complexity

**Time O(n²), space O(1).**

Roughly n²/2 swaps for the transpose, then n rows × O(n) to reverse each: two
O(n²) passes composed, which is still O(n²). There are n² cells and you must
touch essentially all of them, so O(n²) is the floor, not a failure — don't burn
box time hunting for something faster that doesn't exist.

The space is the whole point. Building a rotated copy is also O(n²) time and
just as correct, but it costs O(n²) space and fails the constraint.

## Gotchas

- **Looping `j` over all of `range(n)`.** Every pair swaps twice, the matrix is
  unchanged, and it looks correct. The single most common way to lose this.
- **Sanity check to run in your head:** after the transpose loop, `matrix[0]`
  should be the old first *column*. If it isn't, the bound is wrong.
- `row[::-1]` builds a **new** list — O(n) space, not in place. `row.reverse()`
  mutates and returns `None`.
- `matrix.reverse()` reverses the **order of the rows**, not their contents.
  Both are useful here; confusing them silently gives a different rotation.
- `list(zip(*matrix))` is a lovely one-line transpose and you should know it
  exists — then don't use it when the problem says in place, because it
  allocates a whole new structure and reaching for it signals you didn't read
  the constraint.
- `rows, cols = len(matrix), len(matrix[0])` on the first line of every matrix
  problem. Swapping them is invisible on square tests and an instant
  `IndexError` on a 2×5.

## Related

- [[layer-by-layer-traversal]] — the other matrix-index discipline: that one
  walks a grid without changing it, this one rewrites it without walking a path
- [[in-place-marker]] — the same O(1)-space instinct, but storing *state* in the
  input rather than merely rearranging it
- [[two-pointers]] — reversing a row is the degenerate opposite-end case
- [[loop-invariant-bounds]] — the `j > i` bound is exactly a loop invariant, and
  getting it wrong here fails silently rather than loudly

## Evidence

Problems where I used this unaided:
