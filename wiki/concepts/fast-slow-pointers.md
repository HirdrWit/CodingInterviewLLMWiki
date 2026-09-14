---
type: concept
status: unseen
updated: 2026-09-14
sources: [https://www.geeksforgeeks.org/floyds-cycle-finding-algorithm/]
tags: [linked-list, algorithms]
---

# Fast & Slow Pointers

**In one sentence, in my own words** — run two pointers over the same list at
different speeds, or with a fixed head start, and read the answer off where they
end up relative to each other — which answers positional questions in one pass,
on a structure that has no length and no going back.

Pattern D in [[../lessons/06-linked-list|Lesson 6]]. Also called the tortoise and
hare, or Floyd's algorithm in its cycle-detection form.

## When to reach for it

Trigger phrases: "the **middle** of the list", "the **nth from the end**", "is
there a **cycle**", "where does the cycle **begin**", "detect a loop" — and,
crucially, any of those with "**in one pass**" or "**O(1) space**" attached.

The general tell: **a positional question on a structure with no length.** On an
array you'd compute the index. Here there's nothing to compute from, and the
obvious fix (walk once to count, walk again to the position) is two passes. The
two-pointer gap *is* the arithmetic.

## How it works

Three variants, same idea.

**Different speeds — finds the middle.** `slow` moves 1, `fast` moves 2. When
`fast` falls off the end, `slow` has gone exactly half as far.

```
        ┌──┐  ┌──┐  ┌──┐  ┌──┐  ┌──┐
        │ 1│─▶│ 2│─▶│ 3│─▶│ 4│─▶│ 5│─▶ None
        └──┘  └──┘  └──┘  └──┘  └──┘
start:  s,f
step1:        s          f
step2:              s                 f = None → stop; slow is the middle
```

**Fixed offset — finds the kth from the end.** Advance `fast` k steps, then move
both at the same rate. The gap never changes, so when `fast` hits the end, `slow`
is exactly k from it.

**Floyd's cycle detection.** On a circular track, a 2-speed pointer gains exactly
one position per step on a 1-speed pointer. Gaining *one* per step means it can
never jump over — it must land on it. So:

- they meet → **there is a cycle**
- `fast` reaches `None` → **there is no cycle**

## Minimal example

```python
slow = fast = head
while fast and fast.next:      # order matters: `and` short-circuits
    slow = slow.next
    fast = fast.next.next
    if slow is fast:           # `is`, not `==` — identity, not value
        return True
return False
```

## Complexity

O(n) time. **O(1) space** — and the O(1) is the entire reason to use it. The
obvious cycle-detection solution is a [[seen-set]] of visited nodes: also O(n)
time, but O(n) space, and the interviewer's follow-up is always "now do it in
constant space."

## Gotchas

- **The loop condition.** `while fast and fast.next` — both checks, in that
  order. Python short-circuits, which is what stops `fast.next` from exploding
  when `fast` is `None`. Reversed, it crashes on every even-length list.
- **Which middle?** At even length there are two candidates. `[1,2]` — do you
  want `1` or `2`? Starting both at `head` versus starting `fast` at `head.next`
  changes the answer. Decide deliberately and check it by hand.
- **`is` not `==`.** Two different nodes can hold equal values. Cycle detection
  is an identity question.
- **Off-by-one in the offset variant.** Whether `slow` lands on the target node
  or on its *predecessor* depends on whether you advance `fast` k or k+1 steps —
  and for a deletion you want the predecessor. Pair it with a [[dummy-head]] so
  that even removing the head has a predecessor to land on.
- Empty list and single node. Always.

## Related

- [[two-pointers]] — the array-side sibling; there both pointers usually move
  *toward* each other, here they move the same direction at different rates
- [[linked-list]] — the structure whose missing length forces this
- [[seen-set]] — the O(n)-space solution this exists to beat
- [[pointer-rewiring]] — often what you do once fast/slow has located the node

## Evidence

Problems where I used this unaided:
