---
type: concept
status: unseen
updated: 2026-09-14
sources: []
tags: [sliding-window, algorithms]
---

# Window Invariant

**In one sentence, in my own words:** The property a
[[variable-window|window]] is required to have — stated explicitly before any
code — because it is the thing that decides when the left edge moves and where
the answer gets recorded.

Introduced in [[../lessons/03-sliding-window|Lesson 3]]. This page exists because
naming the invariant is the step people skip, and skipping it is how a sliding
window comes out subtly wrong while still passing the examples.

## When to reach for it

Every time you write a [[variable-window|variable window]]. Before the loop,
finish this sentence out loud:

> *"After the shrink loop, the window always satisfies ______."*

If you can't finish it, you don't have an algorithm yet — you have a shape you're
hoping will work.

## How it works: the two shapes

There are exactly two, and they are mirror images. The fork is set by whether the
problem says **longest** or **shortest**.

### Shape 1 — maximizing: *shrink while INVALID*

> "Find the **longest** stretch satisfying P."

Grow greedily. When admitting breaks P, evict until P holds again. The window is
valid only *after* the loop — so that's where you record.

```python
admit(s[right])
while not P:            # invalid → must shrink
    evict(s[left]); left += 1
best = max(best, right - left + 1)     # RECORD AFTER
```

### Shape 2 — minimizing: *shrink while STILL VALID*

> "Find the **shortest** stretch satisfying P."

Grow until P first holds, then squeeze as hard as P allows, recording on every
squeeze. The tightest window is the last one before P breaks — so you record
*inside*.

```python
admit(s[right])
while P:                # still valid → try tighter
    best = min(best, right - left + 1)  # RECORD INSIDE
    evict(s[left]); left += 1
```

**Longest → record after. Shortest → record inside.** Getting this backwards is
the single most common sliding-window bug.

## Making the invariant O(1) to check

The invariant is evaluated on every iteration, so it has to be cheap or the
O(n) claim dies. The move is almost always: **collapse a comparison of
structures into a comparison of integers.**

| Invariant, stated naively | Cheap form to maintain |
|---|---|
| "no repeated characters in the window" | the [[seen-set]] doesn't already hold the incoming char |
| "at most k distinct" | `len(counts) <= k` |
| "at most k replacements needed" | `(right - left + 1) - max_count <= k` |
| "window contains every required char, with multiplicity" | `have == need_distinct`, one integer each side |

That last row is the whole difficulty of the topic's Hard problem: rather than
comparing two dictionaries every step, keep a single counter of *how many
distinct requirements are currently met*, and bump it only at the exact moment a
character's count crosses its requirement.

## Complexity

The invariant check happens O(n) times, so its own cost multiplies straight into
the total. O(1) check → O(n) overall. An O(k) check (`max(counts.values())`,
rebuilding a set, slicing) → O(n·k), which is the anti-pattern.

## Gotchas

- **Never stating it.** Write the invariant as a comment above the loop. It is
  also exactly what you narrate to the interviewer, so it's free.
- **An invariant that isn't actually maintained.** If a count hitting zero isn't
  deleted, `len(counts)` stops meaning "distinct in window" and the condition
  quietly tests something else.
- **Assuming monotonicity that isn't there.** "Growing can only increase the sum"
  requires non-negative values. With negatives the greedy shrink is unsound and
  the whole technique is the wrong tool.
- **Checking the invariant before the admit.** The admit is what can break it;
  check after.

## Related

- [[variable-window]] — the loop this governs
- [[sliding-window]] — the parent technique
- [[frequency-map]] — the usual place the invariant is read from
- [[constraint-bounded-complexity]] — why a 26-key map makes the check O(1) and
  the space O(1) too

## Evidence

Problems where I used this unaided:

*None yet. Add links here — and only then promote the status.*
