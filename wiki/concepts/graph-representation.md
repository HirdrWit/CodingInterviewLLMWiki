---
type: concept
status: unseen
updated: 2026-09-14
sources: [https://www.geeksforgeeks.org/graph-and-its-representations/]
tags: [graphs]
---

# Graph Representation

**In one sentence:** A graph is an abstract idea — nodes and the relationships
between them — and before you can traverse one you have to pick how it's stored;
the choice is almost always an adjacency list, and the first line of most graph
solutions is converting the problem's edge list into one.

Section 3 of [[../lessons/11-graphs|Lesson 11]].

## When to reach for it

Trigger: the problem hands you `n` nodes and a list of pairs, or a grid, or a
set of objects with pointers to other objects — and you need to answer "what are
this node's neighbours?" repeatedly.

The tell that you need a conversion: the input is an **edge list** (a flat list
of pairs) and your algorithm keeps wanting to ask about one node at a time.
Scanning all edges to find one node's neighbours is O(E) per question. Convert
once, O(E), and every question after is O(1).

## How it works

**Adjacency list** — `node → list of neighbours`. Space O(V + E). The default.

**Adjacency matrix** — `m[i][j]` is 1 if an edge exists. Space O(V²). Right only
when the graph is dense, when V is small and fixed, or when "is there an edge
i→j?" is asked constantly — that question is O(1) here and O(degree) in a list.

**Edge list** — `[(u, v), ...]`. Usually the input format. Compact, useless for
traversal.

**Implicit** — a grid, where nodes are cells and edges are the four orthogonal
neighbours. Nothing is stored at all; the structure is computed from coordinates.

## Minimal example

```python
from collections import defaultdict

adj = defaultdict(list)
for u, v in edges:
    adj[u].append(v)
    adj[v].append(u)     # ← undirected only; delete this line if directed
```

## Complexity

| | Build | Neighbours of u | Edge u→v? | Space |
|---|---|---|---|---|
| Adjacency list | O(V + E) | O(degree(u)) | O(degree(u)) | O(V + E) |
| Adjacency matrix | O(V²) | O(V) | **O(1)** | O(V²) |
| Edge list | O(1) | O(E) | O(E) | O(E) |

## Gotchas

- **The reverse-edge line.** Adding it on a directed graph, or omitting it on an
  undirected one, is silent — no crash, just a wrong answer.
- **Isolated nodes don't appear as `defaultdict` keys.** If the problem says
  "nodes 0..n-1", iterate `range(n)`, never `adj.keys()`.
- Touching `adj[x]` on a `defaultdict` *creates* the key. Harmless usually;
  surprising if you later count keys.
- Don't reach for a matrix by default. Real graphs are sparse and O(V²) space
  is a real cost an interviewer will question.

## Related

- [[dfs-graph]] — what the representation exists to feed
- [[bfs-graph]] — same
- [[hash-map]] — the adjacency list *is* a hash map of lists

## Evidence

Problems where I used this unaided:
