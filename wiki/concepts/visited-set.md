---
type: concept
status: unseen
updated: 2026-09-14
sources: []
tags: [graphs]
---

# Visited Set

**In one sentence:** The one thing a graph traversal needs that a tree traversal
doesn't — a record of every node already entered, checked before entering, so a
cycle can't send you round forever.

Section 2 of [[../lessons/11-graphs|Lesson 11]]. The whole conceptual delta
between Topic 7 and Topic 11 lives on this page.

## When to reach for it

Trigger: **the structure can loop back, or a node can be reached by more than
one path.** Both are true of every graph and neither is true of a tree.

The tell in a problem statement: "undirected", "edges", "connections",
"prerequisites", "grid", "neighbours" — anything where there's no root and no
guarantee of one-parent-per-node.

If you've written a tree DFS and are about to point it at a graph, this is the
line you're missing.

## How it works

```python
visited = set()

def dfs(node):
    if node in visited:     # ← the entire difference from tree DFS
        return
    visited.add(node)
    for nxt in adj[node]:
        dfs(nxt)
```

Three forms it takes, depending on what the problem needs:

- **A set** when you only care *whether* you've been here. The default.
- **A map** (`original → the thing I built for it`) when the traversal
  constructs as it goes — the map stops the loop *and* guarantees a node reached
  twice resolves to the same object. This is [[seen-set]] grown up.
- **Mutating the input** — overwriting a grid cell with a sentinel. O(1) extra
  space, but it destroys the caller's data. Say so out loud before doing it.

For directed cycle detection two states aren't enough; see
[[topological-sort]] for the white/grey/black version.

## Minimal example

Grid flood fill, where visited is a set of coordinate tuples:

```python
visited = set()
DIRS = ((-1, 0), (1, 0), (0, -1), (0, 1))

def flood(r, c):
    if (r, c) in visited:
        return
    visited.add((r, c))
    for dr, dc in DIRS:
        nr, nc = r + dr, c + dc
        if 0 <= nr < rows and 0 <= nc < cols:   # bounds BEFORE index
            flood(nr, nc)
```

## Complexity

O(V) space. It's what buys the O(V + E) time bound: without it a traversal
revisits nodes without limit and doesn't terminate at all on a cyclic graph.

## Gotchas

- **Forgetting it entirely.** Infinite loop or `RecursionError`. The defining
  bug of graph problems.
- **In BFS, mark on push, not on pop.** Marking on pop lets the same node be
  enqueued once per incoming edge.
- **In iterative DFS, the check belongs at pop time.** Nodes can legitimately sit
  in the stack more than once.
- **Coordinates must be tuples**, not lists — lists are unhashable.
- Mutating the grid instead of keeping a set is a legitimate space optimisation
  and a legitimate thing to be criticised for. State the trade-off.
- Two states are not enough for directed cycle detection. Collapsing "on the
  current path" and "fully explored" reports cycles in valid DAGs.

## Related

- [[seen-set]] — the same idea from Topic 1, over an array instead of a graph
- [[dfs-graph]] / [[bfs-graph]] — the traversals it makes safe
- [[topological-sort]] — where two states stop being enough

## Evidence

Problems where I used this unaided:
