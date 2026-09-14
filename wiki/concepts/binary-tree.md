---
type: concept
status: unseen
updated: 2026-09-14
sources: [https://www.geeksforgeeks.org/binary-tree-data-structure/]
tags: [trees]
---

# Binary Tree

**In one sentence:** A binary tree is either nothing at all, or a node holding a
value and two binary trees — and because the definition refers to itself, the
code that walks it does too.

Introduced in [[../lessons/07-trees|Lesson 7]].

## When to reach for it

You rarely *choose* a binary tree in an interview — you're handed one. The
trigger is recognising what that hand-over implies:

- **The structure is recursive, so the solution should be.** The moment you find
  yourself writing an explicit stack to walk a tree, ask whether a recursive
  call would have said the same thing in three lines.
- **There is no O(1) random access.** No indices, no arithmetic to a node. Every
  node is reached by walking from the root. If a problem needs repeated lookups
  by value on a plain (non-search) tree, that's O(n) each time, and a
  [[hash-map]] built in one pass may be the real answer.
- **"Subtree" is not a special case.** Any node plus everything below it is a
  complete tree. That's why the same function works at every level.

## How it works

```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right
```

Objects pointing at objects — the same machinery as a linked list, with two
`next` pointers instead of one. Nothing is contiguous in memory.

```
        5          ← root (depth 0)
      /   \
     3     8       ← cover the left half: `8` and its children are a whole tree
    / \
   1   4           ← leaves (no children)
```

**Vocabulary, fixed once:**

| Term | Means | Counts |
|---|---|---|
| Depth of a node | Edges from the root down to it | downward |
| Height of a node | Edges from it down to its deepest leaf | upward |
| **h** | Height of the tree = depth of its deepest node | the variable in every space bound |
| Leaf | A node with no children | — |
| Balanced | Sibling subtrees differ in height by ≤ 1, so `h ≈ log₂ n` | — |
| Skewed | Every node has one child; `h = n` | a linked list in a tree costume |

## Minimal example

The shape that every tree recursion starts from:

```python
def walk(node):
    if not node:          # the base case. Write this line FIRST, every time.
        return
    walk(node.left)
    walk(node.right)
```

`Optional[TreeNode]` in a LeetCode signature is telling you exactly this: the
parameter can arrive as `None`, so the guard is line one.

## Complexity

- **Any full traversal: O(n) time.** Every node visited once.
- **Recursive traversal: O(h) space** — one call frame per node on the current
  root-to-leaf path. *Not* O(1) (the call stack is real) and *not* O(n) in
  general. O(log n) balanced, O(n) skewed. Say both.
- **Searching an arbitrary binary tree: O(n).** No ordering to exploit. Only a
  [[bst-invariant|BST]] buys you better.

## Gotchas

- "O(log n)" is a claim about the tree's **shape**, never about it being a tree.
  Nothing guarantees balance unless the problem says so.
- The empty tree (`root = None`) is a legal input on nearly every tree problem
  and is the first case an interviewer tries.
- Decide whether height is counted in **edges** or **nodes** and stay consistent
  for the whole solution. Mixing them is the classic off-by-one.
- Same values ≠ same tree. `[1,2]` and `[1,null,2]` hold the same values and are
  different trees. Shape counts.

## Related

- [[dfs-traversal]] — the three orders you can walk this in, and what each is for
- [[bfs-level-order]] — walking by level instead of by depth
- [[bst-invariant]] — the extra ordering rule that turns O(n) search into O(h)
- [[recursion-returns-upward]] — the default recursion shape on this structure
- [[hash-map]] — what to build when you need repeated lookups a tree can't give

## Evidence

Problems where I used this unaided:
