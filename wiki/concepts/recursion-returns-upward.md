---
type: concept
status: unseen
updated: 2026-09-14
sources: []
tags: [trees, recursion]
---

# Recursion Returns Upward

**In one sentence, in my own words** — no textbook phrasing: each node answers a
question about **its own subtree** by asking the identical question of its two
children, waiting for their answers, and combining them with a little O(1) work
of its own — so the information flows *up* the tree as return values.

Pattern A in [[../lessons/07-trees|Lesson 7]] (section 5), and the shape section 3
calls **post-order**: you can't combine until both children have returned.

## When to reach for it

Trigger: **"the maximum / minimum / total / count *of the tree*"** — "how deep",
"how many nodes", "is this balanced", "are all nodes X".

The general test, and it's the one worth internalising from the whole topic:

> **Does this node need information from above it, or from below it?**
> Above → a parameter travelling down. Below → a return value travelling up.

If a node can decide its own answer given only what its children report, this is
the pattern. If it needs to know about its ancestors, it isn't — see the contrast
below.

## How it works

Two blanks to fill, and that's the entire template:

```python
def solve(node):
    if not node:
        return <the identity — 0, None, True, float('-inf')>
    left  = solve(node.left)
    right = solve(node.right)
    return <combine left, right, node.val>
```

The base case is **not** an afterthought. It's the identity element of whatever
you're combining, and picking it is half the design:

| Combining with | Identity |
|---|---|
| `+` (counting, summing) | `0` |
| `and` ("are all nodes X?") | `True` |
| `max` | `float('-inf')` |
| building a node | `None` |

Get it wrong and the bug appears only on lopsided trees — which the tree you
sketched by hand almost certainly isn't.

```
      ask 5 ───────────────► "how many nodes below me?"
       │  │
       │  └─► ask 8 ──► 1 + 1 + 1 = 3  ──┐
       └────► ask 3 ──► 1 + 1 + 1 = 3  ──┤
                                          ▼
                        5 answers 1 + 3 + 3 = 7

        questions go DOWN, answers come back UP
```

## Contrast: carrying state *down*

The other direction is Pattern B in the same lesson — extra **parameters**
narrowed for each child:

```python
def solve(node, context):
    if not node:
        return <base>
    return solve(node.left,  <context narrowed for the left>) \
       and solve(node.right, <context narrowed for the right>)
```

A node uses this when it **cannot judge itself** from what's below it. The
canonical case is the `(low, high)` range inherited on the way down a BST —
see [[bst-invariant]].

Practical differences worth saying out loud in an interview:

- **Up** is post-order (work after the recursive calls). **Down** is pre-order
  (work before them, because the children need the narrowed value).
- **Up** composes into a return value. **Down** composes into arguments.
- They are **not exclusive.** The common shape on a Medium is both at once: a
  parameter going down *and* a count coming back up in the same function.
- And a third variant for the Hards: report one thing to the parent, record a
  different thing in a `nonlocal` best. That's Pattern H in Lesson 7 — reach for
  it the moment this pattern "almost works but the return value is wrong".

## Complexity

O(n) time — every node visited once, O(1) work at each. O(h) space for the call
stack, where h is the height: **O(log n) balanced, O(n) skewed.** Say both halves;
"O(1) space" for a recursion is the most-missed complexity in the topic.

## Gotchas

- **Missing base case.** `if not node: return ...` is the first line, before you
  think about the body. Most calls in a small tree *are* the `None` calls.
- **Wrong identity.** `0` where `float('-inf')` belonged passes on a balanced
  example and fails on a one-sided one.
- **Recursing twice into the same child** (`solve(node.left)` used twice inline)
  turns O(n) into O(2^h). Bind `left` and `right` to variables once.
- **Doing the work before the calls** when the answer depends on the children —
  that's pre-order, and it reads the children's values before they exist.
- **Returning the accumulated answer when the parent needed something
  extendable.** Different quantities; split them.

## Related

- [[binary-tree]] — the recursive definition this pattern is exploiting
- [[dfs-traversal]] — the walk; this page is what you *do* at each node
- [[bst-invariant]] — the standard example of the opposite direction, state down
- [[bfs-level-order]] — the alternative when the problem talks about *levels*
  rather than about subtrees
- [[dynamic-programming]] — the same "answer from sub-answers" move, on an
  array instead of a tree

## Evidence

Problems where I used this unaided:
