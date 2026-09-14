---
type: concept
status: unseen
updated: 2026-09-14
sources: [https://www.geeksforgeeks.org/breadth-first-search-or-bfs-for-a-graph/]
tags: [graphs]
---

# BFS on a Graph

**In one sentence:** Expand outward in rings using a queue — all nodes one edge
away, then all two edges away — so the first time you reach a node, you reached
it by the fewest possible edges.

Section 5 of [[../lessons/11-graphs|Lesson 11]].

## When to reach for it

Trigger: **"shortest", "fewest steps", "minimum number of moves", "how many
levels"** — in an **unweighted** graph. This is the one situation in graph work
where DFS is not an acceptable substitute.

Also: "spreading" problems (rot, fire, infection) where you want the number of
rounds, and any problem where several sources spread simultaneously — seed every
source into the queue before the loop and one pass handles them all.

If the edges have weights, BFS's guarantee breaks and you want Dijkstra instead
(Topic 12).

## How it works

```python
from collections import deque

q = deque([start])
visited = {start}            # mark on PUSH, not on pop

while q:
    node = q.popleft()
    for nxt in adj[node]:
        if nxt not in visited:
            visited.add(nxt)
            q.append(nxt)
```

To get distances, either carry them in the queue as `(node, dist)` or process
one whole level per outer iteration:

```python
steps = 0
while q:
    for _ in range(len(q)):      # snapshot the level size FIRST
        node = q.popleft()
        ...
    steps += 1
```

That `len(q)` snapshot is the same trick as [[bfs-level-order|level-order
traversal]] from Topic 7 — the queue grows during the inner loop, so the bound
has to be read before it starts.

## Minimal example

Shortest number of edges from `start` to `target`:

```python
q = deque([(start, 0)])
visited = {start}
while q:
    node, dist = q.popleft()
    if node == target:
        return dist          # first arrival IS the shortest
    for nxt in adj[node]:
        if nxt not in visited:
            visited.add(nxt)
            q.append((nxt, dist + 1))
return -1
```

## Complexity

**O(V + E)** time, **O(V)** space — identical to [[dfs-graph|DFS]]. The queue
can hold up to V nodes; the widest level of the graph is the practical bound.

## Gotchas

- **`list.pop(0)` is O(n).** Using a list as a queue silently makes BFS O(V²).
  Always `collections.deque`.
- **Marking visited on pop instead of on push.** A node reachable from three
  neighbours gets enqueued three times. Terminates, but the queue bloats and the
  complexity argument stops being true.
- Reading `len(q)` *inside* the inner loop instead of snapshotting it — the
  level boundary is lost and the distance count goes wrong.
- BFS's shortest-path guarantee is **unweighted only**. Say the word
  "unweighted" out loud when claiming it.

## Related

- [[dfs-graph]] — pick it when the question is about structure, not distance
- [[bfs-level-order]] — the tree version, from Topic 7; this is that plus a
  visited set
- [[visited-set]] — what stops the queue cycling forever
- [[topological-sort]] — Kahn's algorithm is a BFS over in-degrees

## Evidence

Problems where I used this unaided:
