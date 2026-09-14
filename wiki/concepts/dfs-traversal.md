---
type: concept
status: unseen
updated: 2026-09-14
sources: [https://www.geeksforgeeks.org/tree-traversals-inorder-preorder-and-postorder/]
tags: [trees]
---

# DFS Traversal (Pre / In / Post Order)

**In one sentence:** All three orders visit every node exactly once and differ
by a single line's position — *when* you do the work at the current node
relative to recursing into the children — and that position is chosen by which
direction the information has to travel.

Introduced in [[../lessons/07-trees|Lesson 7]].

## When to reach for it

Not "which order do I like" — **which order does the data flow demand?**

| Order | Reach for it when | Trigger phrases |
|---|---|---|
| **Pre** | The node must be handled **before** anything is known about its subtrees, or a value has to be carried **down** | "copy the tree", "serialize", "path from the root", "depends on ancestors" |
| **In** | The tree is a **BST** and you want the values in sorted order | "k-th smallest", "validate the BST", "convert to sorted list", "closest value" |
| **Post** | You need **both children's answers** before you can compute your own | "height", "sum of the subtree", "is my subtree valid", "diameter", anything aggregating upward |

**Post-order is the workhorse.** Most tree problems are "compute something
about my subtree from my children's results", which is post-order by
definition. If you are unsure, guess post.

## How it works

One line moves. That is the entire difference.

```python
def walk(node):
    if not node:
        return
    # PRE-order:  work here — before the children
    walk(node.left)
    # IN-order:   work here — between the children
    walk(node.right)
    # POST-order: work here — after both children have returned
```

On this tree:

```
        5
      /   \
     3     8
    / \   / \
   1   4 7   9
```

| Order | Sequence |
|---|---|
| Pre | 5, 3, 1, 4, 8, 7, 9 |
| In | **1, 3, 4, 5, 7, 8, 9** ← sorted, because this is a BST |
| Post | 1, 4, 3, 7, 9, 8, 5 |

Read pre-order as "me, then my left, then my right"; post-order as "my left, my
right, then me". In-order is the one you cannot derive from a slogan — memorise
that it is **left, me, right**, and that on a BST that is ascending order.

## Minimal example

Post-order, in its purest form — height of a tree:

```python
def height(node):
    if not node:
        return -1                    # identity: an empty tree has height -1 in edges
    return 1 + max(height(node.left), height(node.right))
```

Both recursive calls must return before the `max` can run. That's post-order
without ever writing the word.

## Complexity

**O(n) time** for all three — every node is visited exactly once, with O(1)
work at each.

**O(h) space**, the call stack: at the deepest moment there is one frame per
node on the current root-to-leaf path. O(log n) balanced, **O(n) skewed**.

Iterative versions swap the implicit call stack for an explicit one — the same
O(h), just visible. Worth knowing they exist (in-order iterative with a stack is
the one that gets asked), not worth writing unless asked.

## Gotchas

- **Saying "O(log n)" for a traversal.** Traversal is O(n). O(log n) is for
  *searching* a balanced [[bst-invariant|BST]], a different operation.
- **Saying the recursion is O(1) space.** The call stack is real. O(h).
- **Choosing the order by feel.** If the node needs its children's answers,
  pre-order cannot work — the children haven't returned yet. The dependency
  picks the order.
- **Forgetting the base case value is the identity.** Counting → `0`. "All
  nodes satisfy X" → `True`. Maximum → `float('-inf')`. Height → `-1` or `0`
  depending on edges vs nodes. Get it wrong and the bug only shows on lopsided
  trees, which your hand-drawn example won't be.
- **Collecting the whole in-order list when you only needed the first k.** Walk
  lazily (a generator, or a counter) and stop early.

## Related

- [[binary-tree]] — the structure being walked
- [[recursion-returns-upward]] — post-order restated as a reusable code shape
- [[bst-invariant]] — why in-order on a BST is sorted
- [[bfs-level-order]] — the other traversal, when the problem cares about levels
- [[seen-set]] — what DFS needs the moment the structure can have cycles
  (i.e. graphs); a tree cannot, which is why tree DFS needs no visited set

## Evidence

Problems where I used this unaided:
