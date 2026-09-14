---
type: concept
status: unseen
updated: 2026-09-14
sources: [wiki/lessons/17-math-geometry.md]
tags: [math-geometry, matrix]
---

# Layer-by-Layer Traversal

**In one sentence:** Stop thinking of a spiral as one continuous motion and
think of it as four straight walks on repeat — keep four numbers naming the
walls of the rectangle you haven't visited yet, walk a wall, retire it, and loop
while the rectangle is still non-empty.

Pattern B in [[../lessons/17-math-geometry|Lesson 17]].

## When to reach for it

Trigger: "spiral", "in clockwise order", "layer by layer", "ring by ring",
"peel the outer border".

Generally: any traversal whose *shape* is a shrinking rectangle. The alternative
framing — "visit cells, mark them visited, turn right when you'd hit something
visited" — also works and is worth naming aloud as the brute force, but it costs
O(m·n) space for the visited grid. Offering the brute force and then doing it
with boundaries is exactly the contrast an interviewer is listening for.

## How it works

Four variables are the entire state. They name the walls of the unvisited
rectangle, and each one moves inward the moment its wall has been consumed.

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
    # 1. walk left→right along row `top`,     then top    += 1
    # 2. walk top→bottom down column `right`, then right  -= 1
    # 3. walk right→left along row `bottom`,  then bottom -= 1
    # 4. walk bottom→top up column `left`,    then left   += 1
```

**The loop condition is `and`, not `or`, and `<=`, not `<`.** `<=` because a
single remaining row (`top == bottom`) still has cells to emit. `and` because
the rectangle is empty the moment *either* dimension collapses.

## The guard, which is the actual difficulty

Steps 3 and 4 need a guard, and forgetting it is how this problem gets lost.

After steps 1 and 2 have run, the walls may have **already crossed**. Walking
back along a row that no longer exists emits its cells a second time.

Picture a `1 × 4` input. Step 1 consumes the only row and sets `top = 1`;
`bottom` is still `0`. The outer condition was checked before step 1 and isn't
re-checked until the bottom of the loop, so step 3 happily walks that same row
backwards and emits all four values again.

```python
    if top <= bottom:      # before the right→left pass along `bottom`
        ...
    if left <= right:      # before the bottom→top pass up `left`
        ...
```

The symptom is always **duplicated values in the middle of the output**, and the
failing inputs are always a single leftover row or a single leftover column —
odd dimensions, or a `1 × n` / `n × 1` input.

**Test a 3×3 and a 1×4 by hand before you say you're done.** That sentence is
the concept. There is no cleverness here to compensate for skipping it.

## Complexity

**Time O(m·n), space O(1) extra.**

Every cell is emitted exactly once, which is the floor for anything that reads
the whole grid. The four boundary variables are constant space; the output list
isn't counted against the space budget, since producing it is the task.

The visited-grid alternative is the same O(m·n) time but O(m·n) space — the
whole reason to prefer boundaries.

## Gotchas

- **Missing the two guards.** Duplicated middle values on odd dimensions.
- `<` instead of `<=` in the while condition: silently drops the final single
  row or column.
- `or` instead of `and`: keeps looping after one dimension has collapsed.
- **`rows` and `cols` swapped.** Invisible on square tests, `IndexError` on a
  2×5. Write `rows, cols = len(matrix), len(matrix[0])` first, always.
- Moving a wall *before* finishing its walk. Retire the wall at the end of the
  pass, not the start.

## Related

- [[matrix-in-place-transform]] — the same index discipline, but rewriting the
  grid rather than reading a path across it
- [[in-place-marker]] — the third matrix problem; boundaries are state *beside*
  the data, markers are state *inside* it
- [[loop-invariant-bounds]] — the four walls are a loop invariant, and the guard
  is what keeps it true mid-iteration
- [[opposite-end-pointers]] — `top`/`bottom` and `left`/`right` are two
  converging pointer pairs, run in two dimensions at once
- [[bfs-level-order]] — also processes a structure a layer at a time, but the
  layers come from a queue, not from arithmetic on boundaries

## Evidence

Problems where I used this unaided:
