---
type: concept
status: unseen
updated: 2026-09-14
sources: [https://www.geeksforgeeks.org/reverse-a-linked-list/]
tags: [linked-list, data-structures]
---

# Pointer Rewiring

**In one sentence, in my own words** — editing a linked structure by reassigning
`next` fields in a deliberate order, always saving a reference before the
assignment that would destroy it.

Pattern A in [[../lessons/06-linked-list|Lesson 6]]. There is no algorithm here.
The entire difficulty is not losing your grip on a node.

## When to reach for it

Trigger: "reverse the list", "in-place", "O(1) extra space", "rearrange the
nodes" — any time the problem says *rearrange* rather than *return the values*.

The tell that you're in this territory: the answer involves moving no data at
all. Nodes stay exactly where they are in memory; only the arrows change.

## How it works

The invariant: **the only nodes that exist for you are the ones a variable
currently points at.** Reassigning a `next` field severs a link, and a severed
link is unrecoverable unless something else already holds the far side.

So the rule is **save, then break**:

```
nxt = curr.next     # save   — the forward path, before it's destroyed
curr.next = prev    # break  — flip the arrow backwards
prev = curr         # advance
curr = nxt          # advance
```

Three references at all times. `prev` is what you've already reversed, `curr` is
what you're flipping, `nxt` is the only remaining handle on the unprocessed rest.

## Minimal example

Reversing `1 → 2 → 3`, one round:

```
before:  None    ┌───┐    ┌───┐    ┌───┐
         prev    │ 1 │───▶│ 2 │───▶│ 3 │──▶ None
                 └───┘    └───┘    └───┘
                 curr

save:    nxt = 2
flip:    None ◀──│ 1 │    │ 2 │───▶│ 3 │
                  curr     nxt        ← 1 is detached, but nxt holds 2

after:   None ◀──│ 1 │    │ 2 │───▶│ 3 │
                  prev     curr
```

Loop ends when `curr is None`. **The new head is `prev`.**

## Complexity

O(n) time — one pass, constant work per node.
**O(1) space** — three references regardless of list length. That constant space
is the entire reason this pattern is worth learning; the recursive version is
O(n) *stack* space and does not qualify.

## Gotchas

- **Breaking before saving.** The silent killer. Produces a truncated list or an
  infinite loop, never an error message.
- **Returning the wrong variable.** `curr` is `None`, `head` is now the tail. The
  answer is `prev`. Both wrong returns look plausible until tested.
- **Doing the four lines out of order.** They are one unit; memorise the unit
  rather than re-deriving under pressure.
- **Forgetting to terminate.** After any split or rearrangement some node must
  end with `next = None`, or you've built a cycle and the harness hangs.
- **Claiming O(1) space for a recursive solution.** It's O(n). The interviewer is
  waiting for exactly this.
- Python's `a, b = b, a` evaluates the whole right-hand side first, so
  `prev, curr = curr, nxt` is safe on one line — but the `nxt = curr.next` save
  still has to happen before the flip.

## Related

- [[linked-list]] — the structure this operates on
- [[dummy-head]] — removes the *other* class of linked-list special case (the
  first node); this one handles the *middle*
- [[fast-slow-pointers]] — often finds the node where rewiring should begin

## Evidence

Problems where I used this unaided:
