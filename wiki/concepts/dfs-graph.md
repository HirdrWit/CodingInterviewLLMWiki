---
type: concept
status: unseen
updated: 2026-09-14
sources: [https://www.geeksforgeeks.org/depth-first-search-or-dfs-for-a-graph/]
tags: [graphs]
---

# DFS on a Graph

**In one sentence:** Tree DFS with a [[visited-set|visited set]] bolted on —
dive as deep as one path goes before backtracking, and refuse to enter a node
you've already entered.

Section 5 of [[../lessons/11-graphs|Lesson 11]].

## When to reach for it

Trigger: the question is about **structure**, not distance. "Does a path exist",
"how many components/islands/groups", "is there a cycle", "is it connected",
"is this a valid tree", "flood this region".

Also the default when you need to *build* something while traversing, or when a
recursive formulation is obviously shorter than the queue version.

**Not** for shortest path in an unweighted graph — that's [[bfs-graph]], and
this is the one case where the choice is not free.

## How it works

Recursive form — structurally identical to the tree version except the base case
and the neighbour loop:

```python
visited = set()

def dfs(node):
    if node in visited:
        return
    visited.add(node)
    for nxt in adj[node]:
        dfs(nxt)
```

Iterative form, when the graph might be deep:

```python
stack = [start]
while stack:
    node = stack.pop()
    if node in visited:
        continue          # ← the check must be here, not only at push time
    visited.add(node)
    stack.extend(adj[node])
```

For a disconnected graph, wrap it:

```python
for node in range(n):
    if node not in visited:
        dfs(node)          # each entry to this branch = one new component
```

## Minimal example

Counting connected components:

```python
count = 0
for node in range(n):
    if node not in visited:
        count += 1
        dfs(node)
```

## Complexity

**O(V + E)** time — each node entered once (the visited set blocks the rest),
each edge examined at most twice in an undirected graph.

**O(V)** space — the visited set, plus the recursion stack or explicit stack,
which reaches V deep on a path-shaped graph.

## Gotchas

- **No visited set = infinite loop.** The defining bug of graph work.
- **Recursion depth.** Python's limit is ~1000. A long path graph raises
  `RecursionError` on otherwise-correct code. Know the iterative form, or say
  you'd convert.
- The iterative version can push a node several times before popping it — that's
  fine as long as the `if node in visited: continue` guard is at pop time.
- Iterative DFS visits neighbours in reverse order versus the recursive version
  (a stack is LIFO). Irrelevant for most questions; not for all.
- A disconnected graph needs the outer loop. One call answers a question about
  one component.

## Related

- [[bfs-graph]] — same traversal, queue instead of stack; use it when the answer
  is a distance rather than a fact about structure
- [[visited-set]] — the one thing that makes this work on a graph
- [[topological-sort]] — DFS with three states, or Kahn's BFS
- [[graph-representation]] — what `adj` has to be first

## Evidence

Problems where I used this unaided:
