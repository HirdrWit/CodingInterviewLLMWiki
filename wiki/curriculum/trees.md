---
type: curriculum
status: unseen
updated: 2026-09-14
sources: [https://neetcode.io/practice/practice/blind75]
tags: [curriculum, trees]
---

# Topic 7 — Trees

**Lesson:** [[../lessons/07-trees|Lesson 7 — Trees]]
**Skill:** [[../skills/data-structures|Data Structures]]

11 problems — the largest topic in the Blind 75, and the one that
[[backtracking|Topic 9]], [[tries|Topic 10]] and [[graphs|Topic 11]] are all
built on. Worked in this order: the four Easies are one continuous idea and
belong in a single sitting; the Mediums introduce one new recursion shape each;
the two Hards come last and are expected to take more than one attempt.

| # | Problem | Difficulty | Pattern | Attempts | Status | Write-up |
|---|---|---|---|---|---|---|
| 1 | Invert Binary Tree | Easy | recursive structural transform | 0 | `unseen` | — |
| 2 | Maximum Depth of Binary Tree | Easy | recursion returning upward | 0 | `unseen` | — |
| 3 | Same Tree | Easy | parallel recursion on two trees | 0 | `unseen` | — |
| 4 | Subtree of Another Tree | Easy | tree-equality check at every node | 0 | `unseen` | — |
| 5 | Lowest Common Ancestor of a BST | Medium | BST invariant to pick a direction | 0 | `unseen` | — |
| 6 | Binary Tree Level Order Traversal | Medium | BFS with a queue | 0 | `unseen` | — |
| 7 | Validate Binary Search Tree | Medium | pass bounds down the recursion | 0 | `unseen` | — |
| 8 | Kth Smallest Element in a BST | Medium | in-order traversal is sorted | 0 | `unseen` | — |
| 9 | Construct Binary Tree from Preorder and Inorder Traversal | Medium | traversal-order reconstruction | 0 | `unseen` | — |
| 10 | Binary Tree Maximum Path Sum | Hard | return one value upward, record another globally | 0 | `unseen` | — |
| 11 | Serialize and Deserialize Binary Tree | Hard | traversal with explicit null markers | 0 | `unseen` | — |

The **Pattern** column is filled in here for tracking, but read it *after* a
timed attempt, never before. Knowing "this one passes bounds down the recursion"
going in removes the exact skill the interview tests: recognising it yourself.

## Concepts this topic introduces

- [[../concepts/binary-tree|Binary Tree]] — the recursive definition, and the vocabulary
- [[../concepts/dfs-traversal|DFS Traversal]] — pre/in/post, and what each is *for*
- [[../concepts/bfs-level-order|BFS / Level Order]] — the queue and the level-size trick
- [[../concepts/recursion-returns-upward|Recursion Returns Upward]] — the default shape, and its inverse
- [[../concepts/bst-invariant|BST Invariant]] — ordering as a direction-picker

## Topic done when

- [ ] All 11 solved at least once
- [ ] All 11 re-solved **cold** (no notes, no hints) on a later day
- [ ] Can state time and space complexity for each without thinking — including
      the O(h) call-stack cost and the balanced-vs-skewed caveat
- [ ] Can write the pre-, in-, and post-order sequences of a 7-node tree from
      memory, and say what each order is used for
- [ ] Can explain, to a rubber duck, why in-order on a BST comes out sorted
- [ ] Can explain why validating a BST against the parent alone is wrong
- [ ] Both Hards attempted at least twice, whether or not they were finished

## Notes

*Accumulates as problems get worked — recurring mistakes, insights that transfer.*
