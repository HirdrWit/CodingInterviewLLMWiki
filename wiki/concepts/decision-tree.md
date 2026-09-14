---
type: concept
status: unseen
updated: 2026-09-14
sources: []
tags: [backtracking, recursion]
---

# Decision Tree

**In one sentence, in my own words** — the tree you never actually build, where
a node is a partial answer, an edge is one decision, and a leaf is either a
finished answer or a dead end; recursion walks it depth-first and the call stack
is the path from the root to wherever you're standing.

Taught in [[../lessons/09-backtracking|Lesson 9]].

## When to reach for it

Trigger: you're about to write recursive enumeration and you can't see the
structure. **Draw the tree for a tiny input first.** Three or four elements is
enough. Two minutes with a pen answers the three questions that otherwise cost
fifteen minutes of debugging:

- **What is one decision?** → that's an edge, and it tells you what the loop or
  the pair of recursive calls iterates over.
- **What makes a node complete?** → that's the base case.
- **What's the branching factor and the depth?** → that's the complexity, handed
  to you for free, before you've written a line.

It's also the fastest way to spot that two branches are producing the same
answer — visible symmetry in the drawing means a prune is available.

## How it works

Subsets of `[1,2,3]`, branching on take-it-or-skip-it:

```
                        []
              take 1 ↙        ↘ skip 1
            [1]                  []
       ↙        ↘           ↙        ↘
    [1,2]        [1]      [2]         []
    ↙   ↘        ↙  ↘     ↙  ↘       ↙  ↘
[1,2,3] [1,2] [1,3] [1] [2,3] [2]  [3]  []
```

Depth 3 (one level per decision), branching 2, so 2³ = 8 leaves.

The `path` list in a backtracking solution **is the current root-to-node path**
— the sequence of edges taken to get here. Appending walks down an edge;
popping walks back up one. Moving from `[1,2,3]` to `[1,3]` in the drawing means
undoing two decisions, which is exactly two pops.

Where the answers live varies by problem: sometimes only leaves are answers
(permutations), sometimes **every node** is (subsets). Ask which before writing
the base case.

## Minimal example

Reading the complexity straight off the tree, with no code:

```
"all orderings of n distinct items"
  root has n choices, next level n-1, then n-2 ...
  → leaves = n × (n-1) × ... × 1 = n!
  → depth = n
  → O(n · n!) with the O(n) copy at each leaf
```

## Complexity

Nodes ≈ **bᵈ** for branching factor `b` and depth `d`; that count *is* the time
complexity, times the per-leaf work.

The tree is never materialised — only one root-to-leaf path exists at any
instant — so the auxiliary space is **O(d)**, not O(bᵈ). Confusing "the tree is
exponential" with "the memory is exponential" is a common and visible mistake.

## Gotchas

- Branching factor isn't always constant. Combinations shrink it by one per
  level; grid DFS is 4 at the first step and 3 thereafter (you never step back
  where you came from). Say "at most b" when it varies.
- Drawing the tree for the *full* input instead of a 3-element one. The point is
  to see the shape, not to enumerate.
- Assuming the leaves are the answers when the problem wants every node.
- Forgetting that repeated identical subtrees mean the problem is really DP —
  a decision tree with *overlapping* subproblems should be memoised, not
  re-walked.

## Related

- [[backtracking]] — the procedure that walks this tree; that page is the *how*,
  this one is the *picture*
- [[state-restoration]] — what walking back up an edge costs you in code

## Evidence

Problems where I used this unaided:
