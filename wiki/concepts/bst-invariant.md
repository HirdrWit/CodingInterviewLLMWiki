---
type: concept
status: unseen
updated: 2026-09-14
sources: [https://www.geeksforgeeks.org/binary-search-tree-data-structure/]
tags: [trees, bst]
---

# BST Invariant

**In one sentence, in my own words** — no textbook phrasing: in a binary search
tree, **everything** in a node's left subtree is less than it and **everything**
in its right subtree is greater — not just the two children, the whole subtrees —
and that one rule is why in-order traversal comes out sorted and why a comparison
at a node lets you throw away half the tree.

Patterns F and G in [[../lessons/07-trees|Lesson 7]].

## When to reach for it

Two distinct triggers, and they want different code:

- **"k-th smallest", "validate this BST", "convert to a sorted list", "closest
  value"** → in-order traversal. The values *arrive* sorted; count them off and
  stop early.
- **"search", "insert", "lowest common ancestor"** — anything looking for a
  *place* → compare at the node and descend into **one** child. O(h), not O(n).

The tell for the second: you never need to visit every node. If you catch
yourself writing two recursive calls on a BST search problem, one of them is
unreachable and the comparison should have told you which.

The generalisation worth carrying: **a BST problem is usually a sorted-array
problem in disguise**, so everything from [[binary-search]] is available.

## How it works — and the trap

The invariant is about **subtrees, not children.** This is the single most common
wrong answer in tree interviews.

```
            5
          /   \
         3     8
        / \
       1   6        ← 6 > 3, so it is a legal RIGHT child of 3 ...
                      ... but 6 > 5 and it sits in 5's LEFT subtree.
                      NOT a BST.
```

Compare each node to its parent only, and that tree passes. The check has to be
against an **inherited range**, not one value: the root may be anything, the left
child inherits "and also less than 5", and *its* right child inherits both bounds
at once — `3 < x < 5`, which rules 6 out.

That range travels **down** as parameters — the classic instance of carrying
state downward, the opposite of [[recursion-returns-upward]]:

```python
def check(node, low, high):
    if not node:
        return True                      # an empty subtree is a valid BST
    if not (low < node.val < high):
        return False
    return check(node.left,  low, node.val) \
       and check(node.right, node.val, high)

# start with the range unbounded on both sides
check(root, float('-inf'), float('inf'))
```

Each child gets the *narrower* of what it inherited and what this node imposes:
going left tightens the upper bound to `node.val`, going right tightens the lower.

## Why in-order comes out sorted

In-order is: whole left subtree, then me, then whole right subtree. The invariant
says exactly that everything in the left subtree is smaller than me and everything
in the right is bigger. So the three chunks emit in ascending order — and by
induction each chunk is itself sorted. Not a coincidence, a restatement.

```
        5           in-order: 1, 3, 4, 5, 7, 8, 9
      /   \
     3     8        the second validation strategy: walk in-order and check
    / \   / \       each value is strictly greater than the previous one.
   1   4 7   9      One `prev` variable, no ranges.
```

Both validation strategies are correct; the ranges version is easier to defend
out loud, the in-order version is shorter. Know both, pick one.

## Complexity

Search / insert / delete: **O(h)** time, O(h) space. Balanced → O(log n); skewed
→ O(n), because the invariant still holds but the shape ruined it.

Any full traversal (in-order included): O(n) time, O(h) space.

> **"O(log n)" is a claim about the tree's *shape*, never about it being a BST.**
> The BST gives you the *direction*; balance gives you the *speed*. If asked how
> to guarantee O(log n), name a self-balancing tree — AVL, red-black — you will
> not implement one in 30 minutes but naming one is the clean answer.

## Gotchas

- **Validating against the parent only.** The defining bug. Ranges, not values.
- **`<=` vs `<`.** Decide whether duplicates are allowed and be consistent on
  both sides; the usual LeetCode convention is *strictly* less / greater.
- **Using `float('-inf')` / `float('inf')` as the initial bounds** rather than
  `None` sentinels — simpler, and safe as long as the values are numbers.
- **Collecting the whole tree and calling `sorted()`.** O(n log n), and it throws
  away the structure you were handed. The point of in-order is that the sort is
  already done.
- **Forgetting you can stop early.** k-th smallest doesn't need the other n−k
  nodes; a generator with `yield from` or a counter that returns on hitting k.
- **Assuming a BST is balanced.** A sorted insert sequence builds a linked list.

## Related

- [[binary-tree]] — the structure, before the ordering rule is imposed
- [[dfs-traversal]] — in-order is one of its three positions
- [[recursion-returns-upward]] — the *other* direction; the range here travels
  down as a parameter rather than up as a return value
- [[binary-search]] — same halving logic, on an array; a BST is that logic made
  into a data structure
- [[heap]] — the other ordered tree, and a useful contrast: a heap orders only
  parent-vs-child, which is why a heap is *not* sorted in-order

## Evidence

Problems where I used this unaided:
