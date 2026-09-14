---
type: concept
status: unseen
updated: 2026-09-14
sources: [https://www.geeksforgeeks.org/topological-sorting/]
tags: [graphs, advanced-graphs]
---

# Topological Sort

**In one sentence, in my own words** — no textbook phrasing: line the nodes of a
directed graph up so that **every edge points forwards** in the line — which is
possible **if and only if** there's no cycle, so "give me a valid order" and
"is this even satisfiable?" are the same computation wearing two hats.

Patterns D and E in [[../lessons/11-graphs|Lesson 11]]; section 3.1 of
[[../lessons/12-advanced-graphs|Lesson 12]] covers the choice between the two
implementations.

## When to reach for it

Trigger: **dependency language.** "prerequisites", "depends on", "must come
before", "can you finish all the courses", "in what order can these be
installed", "is it possible to complete".

The modelling move behind all of them, and it's worth saying aloud before you
write anything:

> **"A must come before B" is a directed edge A → B.**

Two shapes of question, one algorithm:

- "**Can** it all be done?" → is the graph acyclic?
- "**In what order?**" → produce the linear ordering.

A strong confirming tell: **several answers are valid.** If the problem's example
notes that other orderings would also be accepted, you're in topo-sort territory.
Unordered pairs have no edge between them, so they can come out either way round.

## How it works — Kahn's algorithm (the BFS one)

Count how many edges point *into* each node — its **in-degree**. In-degree 0
means nothing is blocking it, so it can go now. Queue those. Pop one, append it
to the output, and decrement the in-degree of everything it points at; anything
that drops to 0 just became available, so queue it.

```
   courses: 0 → 1, 0 → 2, 1 → 3, 2 → 3

   in-degree:  0:0  1:1  2:1  3:2
   queue: [0]                    output: []
   pop 0 → 1,2 drop to 0         output: [0]        queue: [1,2]
   pop 1 → 3 drops to 1          output: [0,1]      queue: [2]
   pop 2 → 3 drops to 0          output: [0,1,2]    queue: [3]
   pop 3                         output: [0,1,2,3]
```

```python
from collections import deque

indeg = {u: 0 for u in nodes}
for u in nodes:
    for v in adj[u]:
        indeg[v] += 1

q = deque(u for u in nodes if indeg[u] == 0)
order = []
while q:
    u = q.popleft()
    order.append(u)
    for v in adj[u]:
        indeg[v] -= 1
        if indeg[v] == 0:
            q.append(v)

if len(order) < len(nodes):
    ...          # cycle — no valid ordering exists
```

**The cycle test falls out for free.** Nothing inside a cycle ever reaches
in-degree 0, so nothing inside a cycle ever enters the queue — if fewer nodes came
out than went in, the leftovers are the cycle. That single property is why Kahn's
is usually the better answer even when the question only asks possible/impossible.

## The DFS alternative

Run DFS; when a node's recursion **finishes** (all descendants done), push it onto
a stack. Reverse the stack at the end — that reversed post-order *is* a
topological order, because a node is only pushed after everything it points at.

Shorter to write, and natural if you're already doing a DFS. But it does **not**
detect cycles for free: a plain visited set can't distinguish "looped back onto my
own path" from "reached something I already finished". That needs the three-state
white/grey/black colouring — see [[cycle-detection-dfs-colors]].

| | Kahn's (BFS) | DFS post-order |
|---|---|---|
| Cycle detection | free, `len(order) < V` | needs colours |
| Extra state | in-degree map + queue | recursion stack + colours |
| Recursion depth risk | none, it's iterative | O(V) frames |
| Reads well when | the problem asks about ordering | you're already walking |

Pick one and be able to say why. Kahn's is the default recommendation here.

## Complexity

**O(V + E) time, O(V + E) space** for either version — each node enters the queue
(or the recursion) exactly once, each edge is relaxed exactly once. DFS adds an
O(V) call stack on top.

If the graph is implicit, state the **build cost separately**: turning the raw
input into an adjacency list is often the dominant term, and separating the two
is a good look.

## Gotchas

- **Edge direction backwards.** Produces a perfectly valid topological order *of
  the reversed graph* — a wrong answer that looks completely right. Write out one
  concrete pair before coding: does `[a, b]` mean a→b or b→a? The problem
  statement's phrasing is the only authority.
- **Forgetting nodes with no edges at all.** They have in-degree 0 and belong in
  the output. Seed the in-degree map from the *node set*, not from the edge list.
- **Building in-degrees from `adj` keys only.** A node that only ever appears as a
  destination never becomes a key; use a `defaultdict` or pre-seed every node.
- **Checking `len(order) < V` and forgetting to act on it.** Most of these
  problems want `[]` or `False` returned, not a partial order.
- **`list.pop(0)` instead of `deque.popleft()`.** O(n) per pop; O(V + E) becomes
  quadratic.
- **Decrementing in-degree more than once per edge** when the input contains
  duplicate edges. Dedupe on build if duplicates are possible.
- **Expecting a unique answer.** Multiple valid orders is the normal case; if the
  problem wants a *specific* one (lexicographically smallest), swap the queue for
  a [[heap]].

## Related

- [[graph-representation]] — the adjacency list this runs on, and where the
  in-degree count is built
- [[bfs-graph]] — Kahn's *is* a BFS; the queue and the ordering discipline are the
  same
- [[dfs-graph]] — the post-order alternative
- [[cycle-detection-dfs-colors]] — what the DFS version needs to match Kahn's free
  cycle test; use it when you're DFS-ing anyway, not as a separate pass
- [[visited-set]] — the two-state version, which is enough for undirected graphs
  and **not** enough here
- [[implicit-graph]] — most topo-sort problems hand you prose, not a graph; the
  modelling is where the difficulty lives

## Evidence

Problems where I used this unaided:
