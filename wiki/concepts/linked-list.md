---
type: concept
status: unseen
updated: 2026-09-14
sources: [https://www.geeksforgeeks.org/data-structures/linked-list/]
tags: [linked-list, data-structures]
---

# Linked List

**In one sentence, in my own words** — a chain of scattered nodes where each one
carries a value and the address of the next, so you can rearrange it for free but
you can never jump — only walk.

Introduced in [[../lessons/06-linked-list|Lesson 6]].

## When to reach for it

Trigger: the problem *hands you* a linked list. That's usually the whole signal —
nobody converts an array into one voluntarily.

The real question the interviewer is asking is the reverse one: **"when would you
choose this over an array?"** Answer: when you do a lot of inserting and deleting
at positions you already hold a reference to, and you never need random access.
Queue-like workloads, LRU caches (with a hash map alongside), free lists. In
almost everything else the array wins, because cache locality beats big-O at
realistic sizes.

## How it works

The node *is* the structure. There is no container:

```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next
```

A "list" is just a variable holding the first node. `None` marks the end. There
is no stored length, no bounds check, and no way back — a singly linked node has
no pointer to its predecessor.

## Minimal example

```
head
 │
 ▼
┌─────┬───┐   ┌─────┬───┐   ┌─────┬──────┐
│  7  │ ●─┼──▶│  2  │ ●─┼──▶│  9  │ None │
└─────┴───┘   └─────┴───┘   └─────┴──────┘
 @4210         @9008         @1112
```

The addresses are unrelated. **That one fact explains the whole complexity
table** — there is no formula from index to address, so there is no jumping.

```python
curr = head
while curr:              # the canonical walk
    ...
    curr = curr.next
```

## Complexity

| Operation | Array | Linked list | Why |
|---|---|---|---|
| Access `i` | O(1) | **O(n)** | no computable address — walk |
| Insert/delete at front | O(n) | **O(1)** | nothing has to shuffle |
| Insert/delete at back | O(1)* | O(n) | must walk to find the tail |
| Insert/delete after a held node | O(n) | **O(1)** | rewrite two pointers |
| Length | O(1) | O(n) | nothing stores it |

Space: value **plus a pointer** per element, scattered across memory. Roughly 2×
an array before Python's per-object overhead, and much worse in practice because
walking it misses cache on every node.

## Gotchas

- **No length, no going back.** Any positional question ("the middle", "kth from
  the end") needs [[fast-slow-pointers]], not arithmetic.
- **Walking off the end** is `AttributeError: 'NoneType' has no attribute 'next'`.
  `while curr` vs `while curr and curr.next` — pick deliberately.
- **`p = p.next` moves your variable, not the list.** Mutating `p.next` moves the
  list. Confusing these is most of the debugging.
- **Empty and single-node lists** break more solutions here than any algorithm
  error. Test `None` and `[1]` every time.
- Dumping the nodes into a Python list to make it easy is O(n) space and misses
  the point of the problem. Fine as a stated brute force.

## Related

- [[pointer-rewiring]] — the discipline for editing one without losing it
- [[dummy-head]] — the trick that removes the first-node special case
- [[fast-slow-pointers]] — how you answer positional questions in one pass
- [[hash-map]] — the other side of the memory-layout story: hash maps buy O(1)
  lookup by *computing* an address; linked lists give up computed addresses
  entirely, which is exactly why they're O(n) to search

## Evidence

Problems where I used this unaided:
