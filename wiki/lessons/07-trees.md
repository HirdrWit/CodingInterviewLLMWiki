---
type: lesson
topic: trees
status: ready
updated: 2026-09-14
sources: [https://www.geeksforgeeks.org/binary-tree-data-structure/, https://www.geeksforgeeks.org/tree-traversals-inorder-preorder-and-postorder/, https://www.geeksforgeeks.org/level-order-tree-traversal/, https://www.geeksforgeeks.org/binary-search-tree-data-structure/]
tags: [lesson, trees]
---

# Lesson 7 — Trees

**Curriculum:** [[../curriculum/trees|Topic 7]] · **Materials:** [[../meta/resources|Resources]]
**Prerequisites:** [[../curriculum/arrays-hashing|Topic 1]] (hash sets),
[[../curriculum/linked-list|Topic 6]] (pointers, `None` discipline)
**Work through in:** two sittings, not one. 90 minutes on sections 1–5, then a
break, then 6–11. This is the largest topic in the list and the one three later
topics are built on.

---

## 1. Why this topic

The obvious reason: 11 of the 75 problems are here. It's the biggest single
block, so by raw arithmetic it's where the most marks are.

The real reason is that **this is where recursion stops being a party trick and
becomes the default tool.** Everything before this topic was solvable with a
loop. A hash map scan is a loop. Two pointers is a loop. Sliding window is a
loop. Linked lists were pointers, but still fundamentally a walk from left to
right.

Trees are the first structure where the loop version is *harder* than the
recursive version, because the structure itself is defined recursively. And
once that clicks, three later topics come nearly free:

- [[../curriculum/backtracking|Topic 9 — Backtracking]] is DFS on a tree you
  never actually build, made of the choices you could make.
- [[../curriculum/tries|Topic 10 — Tries]] is a tree with 26 children per node.
- [[../curriculum/graphs|Topic 11 — Graphs]] is a tree that's allowed to have
  cycles — which is why graph DFS needs the [[../concepts/seen-set|seen-set]]
  from Topic 1 and tree DFS doesn't.

So the payoff isn't 11 problems. It's 11 + 2 + 3 + 6 + 1 = 23, nearly a third
of the list. Spend the time here.

---

## 2. The mental model

### A tree is a node holding two smaller trees

That sentence is the whole topic. Read it again.

A binary tree is either **nothing at all** (`None`), or it is a node with a
value and two children — and **each child is itself a binary tree**, by the
exact same definition.

```
                    ┌───┐
                    │ 5 │              ← the root
                    └─┬─┘
              ┌───────┴───────┐
            ┌─▼─┐           ┌─▼─┐
            │ 3 │           │ 8 │      ← each of these is ALSO a whole tree
            └─┬─┘           └─┬─┘
         ┌────┴────┐       ┌──┴───┐
       ┌─▼─┐     ┌─▼─┐   ┌─▼─┐  ┌─▼─┐
       │ 1 │     │ 4 │   │ 7 │  │ 9 │  ← leaves: both children are None
       └───┘     └───┘   └───┘  └───┘
```

Cover the left half of that picture with your hand. What's left — the node `8`
and everything under it — is a complete, valid binary tree. Nothing about it
knows or cares that it used to be somebody's child. **That self-similarity is
what you exploit.**

### The physical analogy: a company org chart

You're the CEO and someone asks "how many people work here?"

You don't walk the building counting desks. You ask each of your two direct
reports the *identical question*, wait for their numbers, and answer
`1 + left + right`. Each of them does the same thing. The people at the bottom
with no reports answer `1` immediately and that's what stops the whole thing.

Three things are now true, and they're true of nearly every tree problem:

1. **You solve the whole problem by asking the same question of smaller pieces.**
2. **The answer to your piece is built from the children's answers**, plus a
   little work at your own node.
3. **Somebody at the bottom must answer without delegating**, or it never ends.
   That's the base case, and it is almost always `if not node: return <nothing>`.

### The vocabulary, fixed once

```
                    5          ← depth 0, height 3      \
                  /   \                                  |
                 3     8       ← depth 1, height 1       |  height of tree = 3
                / \                                      |  (longest root→leaf)
               1   4           ← depth 2, height 0       |
              /                                          |
             0                 ← depth 2+1 = 3, height 0 /
```

- **Depth** of a node: edges from the **root down** to it. Counts downward.
- **Height** of a node: edges from it down to its **deepest leaf**. Counts upward.
- **Height of the tree** = height of the root = depth of the deepest node. Call
  it **h**; it's the variable in every space complexity you're about to state.
- **Leaf**: a node with no children.
- **Balanced**: every node's two subtrees differ in height by at most 1, so
  `h ≈ log₂ n`.
- **Skewed / degenerate**: every node has one child. It's a linked list wearing
  a tree costume, and `h = n`. This is the worst case that all your complexity
  caveats are about.

```
balanced, n=7, h=2          skewed, n=4, h=3
      4                     1
    /   \                    \
   2     6                    2
  / \   / \                    \
 1   3 5   7                    3
                                 \
                                  4
```

---

## 3. The mechanics — enough to reason about cost

### The node

There is no clever memory layout here. A tree is just objects pointing at
objects, like the linked lists in Topic 6 but with two `next` pointers instead
of one. No contiguity, no index arithmetic, **no O(1) random access** — the only
way to reach a node is to walk to it from the root.

```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right
```

LeetCode gives you this class pre-defined. Do not retype it in the interview;
say "assuming the standard `TreeNode`" and move on.

### The call stack is your data structure

The thing people miss: **recursion isn't free of a data structure — it's a
stack you didn't have to declare.** Every call frame sits on the Python call
stack holding its local variables until its children return.

At the deepest point of a DFS, there is exactly one frame per node on the path
from the root to where you currently are. That path is at most **h** long. So:

> **DFS space is O(h)** — not O(n), and not O(1). This is the single most
> commonly misstated complexity in tree interviews.

Balanced: O(log n). Skewed: O(n). **Say both.**

This also means recursion depth is a real, physical limit. CPython's default is
1000 frames; a 10⁵-node skewed tree blows the stack. Nobody expects you to
handle it in a 30-minute box, but knowing it exists is worth a sentence:
"iteratively if we were worried about stack depth on a degenerate tree."

### The three DFS orders — and what each is *for*

All three visit every node exactly once. The only difference is **when you do
the work at the current node relative to recursing into the children.**

```python
def walk(node):
    if not node:
        return
    # (pre-order:  do the work HERE — before the children)
    walk(node.left)
    # (in-order:   do the work HERE — between the children)
    walk(node.right)
    # (post-order: do the work HERE — after both children have returned)
```

One line moves. That's it. But each position buys you something different:

| Order | Sequence on the tree above | What it's FOR |
|---|---|---|
| **Pre**-order | 5, 3, 1, 4, 8, 7, 9 | You need the node **before** you know anything about its subtrees. Copying/cloning a tree, serialising it, carrying a value *down* the recursion. |
| **In**-order | 1, 3, 4, 5, 7, 8, 9 | **On a BST this is sorted ascending.** That single fact is the whole reason in-order exists in interviews. |
| **Post**-order | 1, 4, 3, 7, 9, 8, 5 | You need **both children's answers** before you can compute your own. Heights, sums, "is my subtree valid", anything aggregating upward. Delete a tree. |

Look at that in-order row. `1, 3, 4, 5, 7, 8, 9`. The tree in section 2 was a
BST, and reading it in-order produced sorted output — not by coincidence, by
construction. See [[../concepts/bst-invariant|the BST invariant]].

**Post-order is the workhorse.** Most tree problems are "compute something
about my subtree from something about my children's subtrees", and that is
post-order by definition. If you're unsure which order a problem wants, post is
the better first guess.

### BFS: the queue and the level-size trick

DFS goes deep. Sometimes the problem cares about **levels** — "return each row",
"how many rows", "the rightmost node of each row". Depth-first visits nodes from
different levels interleaved, so it's the wrong shape. You want
[[../concepts/bfs-level-order|breadth-first]]: a queue.

```
queue: [5]              →  level 0
pop 5, push 3, 8
queue: [3, 8]           →  level 1
pop 3, push 1, 4;  pop 8, push 7, 9
queue: [1, 4, 7, 9]     →  level 2
```

The trick that makes levels come out *separated* rather than as one flat
stream: at the top of each round, **record how many nodes are in the queue
right now**. That count is exactly one level, because everything currently
queued was pushed by the previous level. Pop exactly that many, and whatever
got pushed while you did is the next level.

```python
from collections import deque

q = deque([root])
while q:
    for _ in range(len(q)):     # ← len(q) evaluated ONCE, before the pops
        node = q.popleft()
        ...
        if node.left:  q.append(node.left)
        if node.right: q.append(node.right)
    # one full level just finished here
```

`len(q)` is captured by `range()` before the loop body runs, which is precisely
why this works. Write it that way and it's correct; snapshot it into a variable
if it makes you happier. **Do not use a list with `.pop(0)`** — that's O(n) per
pop and turns your O(n) traversal into O(n²). `deque.popleft()` is O(1).

BFS space is **O(w)** where w is the widest level — up to n/2 for a full tree,
so O(n). Note this is a *different* worst case from DFS's O(h): DFS is bad on
skewed trees, BFS is bad on bushy ones.

---

## 4. Complexity — the table to know cold

| Operation | Time | Space | Why |
|---|---|---|---|
| Any full traversal (pre/in/post/BFS) | **O(n)** | — | Each node visited once, constant work per node |
| DFS recursion | O(n) | **O(h)** | One call frame per node on the current root→leaf path |
| DFS, balanced tree | O(n) | O(log n) | h ≈ log₂ n |
| DFS, skewed tree | O(n) | **O(n)** | h = n. State this caveat. |
| BFS | O(n) | **O(w)** → O(n) | Queue holds one whole level; widest level can be ~n/2 |
| BST search / insert / delete | **O(h)** | O(h) | Halve the search space at each step — *if* balanced |
| BST search, skewed | **O(n)** | O(n) | The invariant still holds; the shape ruined it |
| Find a value in an unsorted binary tree | O(n) | O(h) | No ordering to exploit. Must look everywhere. |

Two readings to take away:

1. **Almost every answer is "O(n) time, O(h) space".** If you catch yourself
   about to say O(log n) *time* for a full traversal, you've confused traversal
   with BST search.
2. **"O(log n)" is a claim about the tree's shape, never about being a BST.**
   A BST gives you the *direction*; balance gives you the *speed*. A skewed BST
   is O(n) for everything. Real libraries fix this with self-balancing trees
   (AVL, red-black); you will not implement one in an interview, but naming one
   is a clean answer to "how would you guarantee O(log n)?"

---

## 5. The patterns

**Each pattern is a trigger in the problem statement → the tool it summons.**
The triggers are the transferable half. There are really only *two* recursion
shapes in this entire topic (A and B), and everything else is a variation on
which order you walk in.

### Pattern A — return a value upward → [[../concepts/recursion-returns-upward|concept page]]
> **Trigger:** "the maximum/minimum/total/count **of the tree**", "how deep", "how many nodes"

The default shape. Each call answers a question about *its own subtree*, built
from the children's answers plus O(1) work at the node.

```python
def solve(node):
    if not node:
        return <the identity — 0, None, True, -inf>
    left  = solve(node.left)
    right = solve(node.right)
    return <combine left, right, node.val>
```

Fill in two blanks — the base case and the combine step — and you're done.
Choosing the base-case value is *not* an afterthought: it's the identity element
for whatever you're combining. Counting → `0`. "Are all nodes X?" → `True`.
Maximum → `float('-inf')`. Get this wrong and the bug appears only on
lopsided trees, which your hand-drawn example won't be.

This is post-order: you can't combine until both children have returned.

### Pattern B — carry state downward
> **Trigger:** "valid according to a rule involving ancestors", "path from the root", "each node must be greater than everything above-left"

Sometimes a node cannot judge itself. It needs context that only its ancestors
know. So you pass that context **down** as extra parameters, and each call
narrows it for its children.

```python
def solve(node, context):
    if not node:
        return <base>
    return solve(node.left,  <context narrowed for the left>) \
       and solve(node.right, <context narrowed for the right>)
```

The classic instance is bounds checking a BST: the root may be anything, but the
left child inherits "and also less than the root", and *its* right child
inherits both constraints at once. `(low, high)` travels down; the node checks
itself against it. See [[../concepts/bst-invariant|bst-invariant]].

**A and B are not exclusive.** Harder problems pass context down *and* return a
value up in the same function. Recognising which direction a given piece of
information has to travel is the actual skill here.

### Pattern C — parallel recursion on two trees
> **Trigger:** "are these two trees the same", "is one a mirror of the other", "merge two trees"

Recurse on **both trees in lockstep**, same position in each:

```python
def compare(p, q):
    if not p and not q: return True        # both ran out together — agree
    if not p or not q:  return False       # one ran out first — different shape
    return p.val == q.val and compare(p.left, q.left) and compare(p.right, q.right)
```

The three-branch base case is the entire pattern. **The order of those first two
lines matters**: check "both None" first, or the second line fires on two empty
subtrees and reports a false mismatch. Structure differences are caught by the
`None`s disagreeing, not by any explicit shape check.

### Pattern D — a check at *every* node
> **Trigger:** "does **any** subtree ...", "contains a subtree such that ..."

Two nested recursions. The outer one visits every node; the inner one runs a
full check rooted at that node. Cost is O(n × m), and **that's usually the
accepted answer** — say the complexity out loud and note that hashing the
serialised subtrees could do better, then move on. Don't build the clever
version unless asked.

The trap: collapsing the two recursions into one function. They're asking
different questions ("is there a match *anywhere below*" vs "is there a match
*exactly here*") and they need separate base cases.

### Pattern E — BFS with a queue → [[../concepts/bfs-level-order|concept page]]
> **Trigger:** the words **"level"**, "row by row", "each depth", "left to right", "shallowest", "minimum depth"

The word *level* in a problem statement is about as loud a signal as this list
gets. Queue, level-size snapshot, one list per round. Section 3 has the shape.

Also the right tool for **shortest path** questions, which is why this pattern
reappears in [[../curriculum/graphs|Topic 11]]: BFS reaches a node by the fewest
edges, so the first time you see your target, you're done.

### Pattern F — in-order on a BST is sorted → [[../concepts/bst-invariant|concept page]]
> **Trigger:** "the k-th smallest/largest", "validate this BST", "convert to a sorted list", "the closest value"

Don't collect the whole tree and sort it — that's O(n log n) and throws away the
structure you were handed. Walk in-order and the values *arrive* in ascending
order; count them off as they come, and you can stop early once you've got what
you need.

The generalisation worth carrying: **a BST problem is usually a sorted-array
problem in disguise**, which means everything from [[../curriculum/binary-search|Topic 5]]
is available to you.

### Pattern G — use the BST invariant to pick a direction
> **Trigger:** "search", "insert", "lowest common ancestor", any BST problem where you're looking for a *place*

The specific BST power: standing at a node, comparing tells you which single
child to descend into, so you **discard half the tree without looking at it**.
O(h) instead of O(n).

The tell that a problem wants this rather than a traversal: you never need to
visit every node. If you catch yourself writing a recursion with *two* recursive
calls on a BST search problem, stop — one of them is unreachable, and the
comparison should have told you which.

### Pattern H — return one thing up, record another globally
> **Trigger:** Hard problems where the answer's shape doesn't match what the recursion needs to return. "maximum path sum", "diameter", "longest sequence anywhere in the tree"

**This is the single highest-value idea in the topic.** It appears on the Hard
problems here and again in DP, and it defeats people who have Pattern A solid.

The situation: what a node must **report to its parent** and what the node
**contributes to the final answer** are two different quantities. A path that
dips down the left subtree, through a node, and back down the right subtree is
a perfectly good candidate answer — but it can't be *extended* by that node's
parent, because a path can only pass through a node once. So the parent can't
be told about it.

```
             node
            /    \
       left       right
         │          │
         └──────────┘
      the best answer here may bend through `node` and use BOTH sides
      but only ONE side can be handed upward for the parent to extend
```

So the function does two jobs:

```python
def solve(root):
    best = float('-inf')            # the answer — lives OUTSIDE the recursion

    def dfs(node):
        nonlocal best
        if not node:
            return <identity>
        left  = dfs(node.left)
        right = dfs(node.right)

        best = max(best, <the bent version using BOTH children>)   # record
        return <the straight version using the BETTER ONE child>    # report

    dfs(root)
    return best
```

Two different expressions, computed at the same node, going to two different
places. When you meet a tree problem where Pattern A "almost works but the
return value is wrong", **this is the fix.** Ask yourself: *what do I need to
tell my parent, and is that the same as the answer?* When it isn't, split them.

### Pattern I — reconstruct from traversal orders
> **Trigger:** "given the preorder and inorder", "rebuild the tree from ..."

Two facts do the work, and the problem is finding them, not coding them:

- **Pre-order's first element is always the root** of whatever range it covers.
- **In-order splits at the root**: everything before it is the entire left
  subtree, everything after is the entire right subtree.

So one order names the root, the other tells you how big each side is; recurse
on the two halves. Why one order alone isn't enough is worth being able to
answer: several distinct trees share the same pre-order.

### Pattern J — traversal with explicit null markers
> **Trigger:** "serialize", "encode this tree as a string and get it back"

Section 2's problem restated: a plain traversal loses the shape, because you
can't tell a missing left child from a missing right one. Fix it by **writing
the `None`s down too** — a sentinel like `"#"` — so the string is unambiguous
and a pre-order walk can rebuild it in one pass, consuming tokens in the order
it needs them. Same idea as the length-prefix protocol in
[[../curriculum/arrays-hashing|Topic 1]]'s Encode/Decode Strings: *the
delimiter must be something the data cannot be.*

---

## 6. Python notes

```python
from collections import deque
from typing import Optional

# LeetCode's signature style — learn to read it, you'll see it on every tree problem
def maxDepth(self, root: Optional[TreeNode]) -> int:
    ...
```

`Optional[TreeNode]` means **`TreeNode` or `None`**. It is not decoration: the
type signature is telling you the base case. Every parameter typed `Optional`
is a parameter that can arrive as `None`, and **`if not node: return ...` is
the first line you write, before anything else.** Make that muscle memory —
write the base case, then think about the body.

```python
q = deque([root])          # O(1) popleft, unlike list.pop(0) which is O(n)
q.popleft(); q.append(x)

nonlocal best              # rebind an enclosing function's variable (Pattern H)
self.best = 0              # the alternative if nonlocal feels fiddly

yield from inorder(node.left)   # generators: in-order lazily, so you can stop early

import sys
sys.setrecursionlimit(10**5)    # know it exists; you won't usually need it
```

Two small things that read well to an interviewer:

- **Nested helper functions.** `def dfs(node):` inside the method, closing over
  the outer variables, beats threading an accumulator through every call.
- **`None`-guard when you push, not when you pop.** In BFS,
  `if node.left: q.append(node.left)` keeps `None` out of the queue entirely.
  The alternative — pushing everything and skipping `None` after popping —
  works too, but pick one and be consistent. Mixing them is how `None` reaches
  `.val` and raises `AttributeError`.

---

## 7. Traps

- **Saying "O(log n)" for a traversal.** Traversing is O(n); *searching a
  balanced BST* is O(log n). Different operations. Interviewers hear the
  difference immediately.
- **Saying DFS space is O(1).** The call stack is real. It's O(h), and O(n) on
  a skewed tree. This is the most-missed complexity in the whole topic.
- **Forgetting the empty tree.** `root = None` is a legal input on basically
  every one of these 11 problems, and it's the first case an interviewer tries.
- **A single node is not a leaf-free tree.** Test `[1]` and a two-node tree.
  Off-by-one in height/depth lives exactly there — decide whether height is
  counted in *edges* or *nodes* and stay consistent.
- **Validating a BST against the parent only.** A node can be greater than its
  immediate parent and still violate the invariant against a grandparent. The
  check is against an inherited *range*, not one value. This is the single most
  common wrong answer in tree interviews.
- **`list.pop(0)` in a BFS.** O(n) per pop; your O(n) traversal is now O(n²).
  Use `deque`.
- **Recomputing `len(q)` inside the level loop.** It changes as you push. Snap
  it once per round.
- **Mutating the tree when the problem said return a new one** (or the reverse).
  Read which is wanted.
- **Confusing "same tree" with "same values".** Shape counts. `[1,2]` and
  `[1,null,2]` hold the same values and are different trees.
- **Skipping the base case because the example had no `None`s.** Draw a
  three-node tree and trace the calls by hand. The `None` calls are the majority
  of calls in any small tree.
- **Global state that isn't reset.** If you use `self.best`, initialise it at
  the top of the *public* method, not at class level. Test harnesses reuse the
  object.

---

## 8. Worked example

Outside the problem set, so nothing is spoiled.

> **Problem.** A node in a binary tree is **good** if no node on the path from
> the root to it has a greater value. Count the good nodes.
>
> ```
>         3
>       /   \
>      1     4
>     /     / \
>    3     1   5
> ```
>
> | Node | Largest value above it | Good? |
> |---|---|---|
> | 3 (root) | — | yes, trivially |
> | 1 (left) | 3 | no — 3 > 1 |
> | 3 (under 1) | 3 | **yes** — nothing above is *greater* |
> | 4 | 3 | yes |
> | 1 (under 4) | 4 | no |
> | 5 | 4 | yes |
>
> Answer: 4.

**Read the trigger.** "No node **on the path from the root**" — the condition
at a node depends on its *ancestors*, not on its subtree. A node cannot judge
itself from what's below it. That's **Pattern B, carry state downward**, and
the state to carry is a single number: the largest value seen on the way here.

Ask the question the section 5 way: *which direction does the information
travel?* Downward. So it's a parameter, not a return value.

```python
def goodNodes(root: Optional[TreeNode]) -> int:
    def dfs(node, max_so_far):
        if not node:                     # base case first, always
            return 0
        is_good = 1 if node.val >= max_so_far else 0
        max_so_far = max(max_so_far, node.val)   # narrowed for BOTH children
        return is_good + dfs(node.left, max_so_far) + dfs(node.right, max_so_far)

    return dfs(root, root.val)           # root is good against itself
```

**Notice the two directions in one function.** `max_so_far` travels *down* as a
parameter (Pattern B); the count travels *up* as a return value (Pattern A).
That combination is extremely common and it's worth naming out loud when you
write it.

**Why `>=` and not `>`.** An equal value doesn't violate "no node is *greater*".
Getting this wrong costs you the third node in the example — and that node is
in the example precisely because the problem author knew it was the trap. When
a sample input contains a tie, the tie is the test.

**Why pre-order.** The work at the node happens *before* recursing, because the
children need the updated `max_so_far`. Compare to Pattern A, where the work
happens after. The order is dictated by which direction the data flows, not by
taste.

**Complexity.** O(n) time — every node visited once, O(1) work each. O(h) space
for the call stack: O(log n) balanced, O(n) skewed. Say it in that form.

**The move to internalise:** before writing a line, ask *does this node need
information from above it, or from below it?* Above → parameter. Below →
return value. Both → both. That one question picks the pattern for most of the
11 problems in this topic.

---

## 9. Materials

Full list on [[../meta/resources|Resources]]. For this lesson, in order:

1. [Binary tree data structure](https://www.geeksforgeeks.org/binary-tree-data-structure/) — 15 min, the vocabulary and the node definition
2. [Tree traversals: inorder, preorder, postorder](https://www.geeksforgeeks.org/tree-traversals-inorder-preorder-and-postorder/) — 20 min. **The key page.** Trace all three by hand on a 7-node tree before moving on.
3. [Level order traversal](https://www.geeksforgeeks.org/level-order-tree-traversal/) — 10 min, BFS and the queue
4. [Binary search tree](https://www.geeksforgeeks.org/binary-search-tree-data-structure/) — 15 min, the invariant and why in-order comes out sorted
5. [`collections.deque`](https://docs.python.org/3/library/collections.html#collections.deque) — skim the docs, note `popleft` is O(1)
6. [NeetCode on YouTube](https://www.youtube.com/@NeetCode) — search the problem name, but **only after** a timed attempt and a debrief

**Do this before section 10:** draw a 7-node tree on paper and write out its
pre-, in-, and post-order sequences from memory. Then make it a BST and check
that in-order comes out sorted. Ten minutes, and it's worth more than another
article.

---

## 10. Self-check

Answer **aloud**, in full sentences, before starting the timer. Anything that
comes out mumbled, go back to section 3.

1. Give the recursive definition of a binary tree in one sentence.
2. What is the difference between the height and the depth of a node?
3. Why is DFS space O(h) and not O(1) or O(n)? When *is* it O(n)?
4. Name what each of pre-, in-, and post-order is *for* — not the order, the use.
5. Why does in-order traversal of a BST produce sorted output?
6. What is the level-size trick in BFS, and why does it work?
7. When does BFS use more space than DFS, and when is it the other way round?
8. What is wrong with validating a BST by comparing each node to its parent?
9. In Pattern H, why can't the node report to its parent the same value it
   contributes to the answer?
10. Which direction does information travel in Pattern A? In Pattern B?
11. Give the trigger phrase for each of patterns A–J.
12. Is a BST guaranteed O(log n) lookup? Defend your answer.

---

## 11. Ready?

**First timed problem: Invert Binary Tree** (Easy) — Pattern A, in its simplest
possible form. Famously the problem Max Howell didn't get at Google. It's four
lines. The point is not the four lines; it's proving the recursive reflex is
installed before you meet the Mediums.

Say `timed invert-binary-tree` and I'll give you the statement and start the
30-minute box. No hints while it's running.

**What "good" looks like here:**

- You write `if not root: return None` **before** anything else, without pausing
- You say out loud "this is a node holding two smaller trees, so I recurse"
  before writing the body
- You state O(n) time and O(h) space, with the balanced-vs-skewed caveat,
  unprompted
- You test `None`, a single node, and a tree with only a left child
- You can say whether your solution is pre- or post-order and why it doesn't
  matter for this one

This is a 5-minute problem. **Do not stop the clock.** Roll straight into
**Maximum Depth**, then **Same Tree**, then **Subtree of Another Tree** — the
four Easies here are one continuous idea and they belong in one box. Getting all
four inside 30 minutes is the target, and it earns you the right to start the
Mediums on a separate day.

Then stop. Eleven problems is three or four sessions, not one. The Hards
(Maximum Path Sum, Serialize and Deserialize) should be attempted **after** the
Mediums are solid, and not finishing them first time is expected — they go in
the [[../meta/review-queue|review queue]] at +2 days and come back.

**Repeat until cold-solvable is the plan, not the fallback.** This topic more
than any other: three later topics are standing on it.
