---
type: concept
status: unseen
updated: 2026-09-14
sources: [wiki/lessons/17-math-geometry.md]
tags: [math-geometry, matrix, space-optimisation]
---

# In-Place Marker

**In one sentence:** When the problem forbids extra space, you still need
somewhere to write down what you found — so pick cells of the input whose
original values the algorithm no longer needs, and overwrite them with flags.

Pattern C in [[../lessons/17-math-geometry|Lesson 17]].

## When to reach for it

Trigger: "modify in place", "**O(1) extra space**", "do not use additional
memory for another array", or — most tellingly — a **follow-up that takes away a
space budget you were already using successfully**.

That last shape is the signal. The obvious solution allocates a set of rows and
a set of columns to clear, costing O(m+n). When the interviewer removes that
budget, the question stops being *how do I compute this* and becomes **"where
can I put m+n bits that I'm allowed to destroy?"** Answer: somewhere in the
input whose values the algorithm is finished with.

This is [[seen-set|Topic 1]]'s trade run backwards. There the move was *spend
memory, buy time*. Here memory is unavailable, so the input doubles as scratch
space — a move that shows up again in bit manipulation, in array-marking tricks,
and in a fair number of Hards.

## How it works

The shape is always **two phases, and they must not interleave**:

```
  PASS 1 — read the whole structure, record findings into the chosen marker cells
  PASS 2 — read the markers, apply the changes
```

Interleaving is the bug that eats this. If you act the instant you find
something, the values you wrote become indistinguishable from values that were
in the input, pass 2 reads its own output as evidence, and the effect cascades
across the whole structure.

So the question to ask every time you store state inside the data is:
**can I still tell a flag apart from real data?** If not, you need a separate
marker channel — one extra variable, or a sentinel value that cannot occur
naturally. One extra boolean is still O(1) space; a whole extra row is not.

## Choosing the marker cells

Two properties make a cell a good marker:

- **Its original value is no longer needed** by the time you write the flag.
- **It already belongs to the thing it flags**, so no index arithmetic is
  needed to find it again.

For a matrix, the **first row and first column** are the natural choice: they
are exactly m+n cells, and cell `(0, j)` naturally stands for "column `j`".

The catch — and the reason this is a Medium rather than an Easy — is that the
two marker lines **overlap at cell `(0,0)`**, which would have to flag both its
row and its column. One cell cannot hold two independent bits, so one of the two
gets promoted to an ordinary variable.

And because the marker cells sit *inside* the data, order of operations matters:
**read everything before you write anything, and handle the marker lines
themselves last**, after they have finished being markers.

## Complexity

**Time O(m·n), space O(1).**

Two full sweeps of the structure, plus constant scratch — typically a single
boolean for the overlapping cell. Two O(m·n) passes compose to O(m·n); there is
no time cost to this technique at all, which is what makes it the right answer
rather than a trade-off.

Compare: the readable version is the same O(m·n) time at O(m+n) space. Say that
one first, then offer this as the follow-up.

## Gotchas

- **Interleaving the marking pass and the applying pass.** The single failure
  mode. Two separate passes, always.
- **Flags indistinguishable from data.** If the flag value can occur naturally
  in the input, the technique is broken before it starts — find a sentinel that
  can't, or a separate channel.
- **Forgetting the overlap.** Two marker lines that share a cell need one extra
  variable. Dropping it loses exactly one row or one column, which passes most
  small test cases.
- **Cleaning the marker lines too early.** They stop being markers only once
  every other cell has read them.
- Mutating a structure you are still iterating. Related trap, same root cause.

## Related

- [[matrix-in-place-transform]] — the other O(1)-space matrix move: rearranging
  without extra storage, rather than *storing* without extra storage
- [[layer-by-layer-traversal]] — keeps its state in four variables beside the
  data; this concept is what you do when even that isn't available
- [[seen-set]] — the allocate-a-set solution this replaces, and the trade being
  run in reverse
- [[twos-complement]] — bit manipulation's version of the same instinct: pack
  the state into the representation you already have
- [[state-restoration]] — also mutates shared state deliberately, but undoes the
  mutation on the way out; in-place markers are permanent by design

## Evidence

Problems where I used this unaided:
