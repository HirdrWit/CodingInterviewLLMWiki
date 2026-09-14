---
type: concept
status: unseen
updated: 2026-09-14
sources: [https://www.geeksforgeeks.org/level-order-tree-traversal/, https://docs.python.org/3/library/collections.html#collections.deque]
tags: [trees]
---

# BFS / Level Order

**In one sentence:** Walk the tree with a queue instead of the call stack, and
snapshot the queue's length at the top of each round — that count is exactly one
level, which is what separates a flat stream of nodes into rows.

Introduced in [[../lessons/07-trees|Lesson 7]].

## When to reach for it

The trigger is about as loud as this list gets: **the word "level"**.

- "return each level", "row by row", "each depth", "left to right"
- "the rightmost node of each row", "the average of each level"
- **"minimum depth"** or "shallowest" — BFS finds it without exploring deep
  branches; DFS would have to walk them all
- **shortest path**, which is why this pattern comes back in graphs: BFS reaches
  a node by the fewest edges, so the first sighting of the target is the answer

The anti-trigger: if the problem is about a *root-to-leaf path* or about
aggregating a subtree, that's [[dfs-traversal|DFS]]. Levels are BFS; paths and
subtrees are DFS.

## How it works

```
queue: [5]              →  level 0 is these 1 node
pop 5, push 3, 8
queue: [3, 8]           →  level 1 is these 2 nodes
pop 3 (push 1,4), pop 8 (push 7,9)
queue: [1, 4, 7, 9]     →  level 2 is these 4 nodes
```

Everything sitting in the queue at the top of a round was pushed by the previous
round — so the current length *is* the width of the current level. Pop exactly
that many; whatever gets pushed while you do is the next level.

```python
from collections import deque

def level_order(root):
    if not root:
        return []
    levels, q = [], deque([root])
    while q:
        level = []
        for _ in range(len(q)):          # len(q) evaluated ONCE, before the pops
            node = q.popleft()
            level.append(node.val)
            if node.left:  q.append(node.left)
            if node.right: q.append(node.right)
        levels.append(level)
    return levels
```

`range(len(q))` captures the count before the body runs — which is precisely why
this works. Snapshot it into a named variable if that reads clearer to you.

## Minimal example

Without the level grouping, BFS is just this:

```python
q = deque([root])
while q:
    node = q.popleft()
    ...
    if node.left:  q.append(node.left)
    if node.right: q.append(node.right)
```

The `for _ in range(len(q))` wrapper is the only thing that turns it into level
order. That's the whole trick.

## Complexity

**O(n) time** — every node enqueued once and dequeued once.

**O(w) space**, where w is the widest level. For a full binary tree the bottom
level holds about n/2 nodes, so this is **O(n)**.

Note this is a *different* worst case from DFS's O(h):

| | Bad on | Space there |
|---|---|---|
| DFS | skewed trees | O(n) |
| BFS | bushy/full trees | O(n) |

Neither dominates. On a balanced tree DFS is O(log n) and BFS is O(n), so DFS
is the cheaper default when the problem doesn't demand levels.

## Gotchas

- **`list.pop(0)` instead of `deque.popleft()`.** `pop(0)` is O(n) because every
  remaining element shifts down. It silently turns an O(n) traversal into
  O(n²) — the classic accidental-quadratic in this topic. Import `deque`.
- **Recomputing `len(q)` inside the level loop.** It grows as you push. Snapshot
  once per round.
- **Pushing `None` into the queue.** Guard when you push
  (`if node.left: q.append(...)`) or filter when you pop — pick one and be
  consistent. Mixing them is how `None.val` raises `AttributeError`.
- **Forgetting the empty-tree guard** before `deque([root])`, which otherwise
  queues a `None`.
- **Reaching for BFS on a problem about subtree aggregation.** Levels don't
  help there; you'd end up simulating DFS badly.

## Related

- [[dfs-traversal]] — the other traversal; depth-first, uses the call stack,
  O(h) space instead of O(w)
- [[binary-tree]] — the structure being walked
- [[bst-invariant]] — irrelevant here: BFS ignores ordering entirely

## Evidence

Problems where I used this unaided:
