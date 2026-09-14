---
type: concept
status: unseen
updated: 2026-09-14
sources: [https://www.geeksforgeeks.org/data-structures/linked-list/]
tags: [linked-list, data-structures]
---

# Dummy Head

**In one sentence, in my own words** — put a throwaway node in front of the list
so the real first node has a predecessor, which deletes the "but what if it's the
head?" special case from the code entirely.

Pattern B in [[../lessons/06-linked-list|Lesson 6]]. Also called a *sentinel*.

## When to reach for it

Triggers, in rough order of how often they show up:

- "**merge**" / "build and return a new list" — you don't know the head until the
  first comparison, and a dummy means you don't have to decide up front
- "**remove** all nodes where…" — the head itself might be the thing removed
- any time you catch yourself writing `if prev is None: head = ... else: ...`

The general tell: **the first element needs a different code path from the rest.**
That's not a property of the problem, it's a property of the list having a
ragged front edge. Manufacture an edge and the asymmetry disappears.

## How it works

```
      dummy
        │
        ▼
    ┌──────┬───┐
    │ junk │ ●─┼──▶  (real list, or the output being built)
    └──────┴───┘

    return dummy.next     ← never dummy
```

Usually paired with a **tail pointer** that trails along at the last node of the
output, so appending is uniform:

```python
dummy = ListNode()
tail = dummy

while ...:
    tail.next = some_node      # attach
    tail = tail.next           # advance

return dummy.next
```

The dummy's `val` is never read. Its only job is to be a node that can hold a
`next`.

## Minimal example

Removing every node equal to `val` — with no special case for the head:

```python
dummy = ListNode(0, head)
prev = dummy

while prev.next:
    if prev.next.val == val:
        prev.next = prev.next.next   # splice out; do NOT advance
    else:
        prev = prev.next

return dummy.next
```

Empty list and all-nodes-match both come out correct without a single extra
branch. That's the payoff: **the edge cases stop being special.**

## Complexity

Free. One extra node — **O(1) space**, not proportional to input. It costs one
allocation and buys the removal of every first-element branch, which is where
the bugs were.

## Gotchas

- **Returning `dummy` instead of `dummy.next`.** A junk `0` at the front of your
  output. The most common way to fail an otherwise correct solution.
- **Forgetting the tail pointer** and trying to walk from `dummy` each time —
  that's O(n²).
- **Advancing `prev` after a splice.** When you remove a node, the new
  `prev.next` is unexamined; advancing skips it. `6 → 6` is the test that catches
  it.
- A dummy head doesn't help with problems that modify *within* the list and
  return the original head — it's for building, filtering, and merging.

## Related

- [[linked-list]] — the structure whose ragged front edge this papers over
- [[pointer-rewiring]] — handles the *middle* of a list; this handles the front
- [[two-pointers]] — the merge that a dummy head is usually collecting the output of

## Evidence

Problems where I used this unaided:
