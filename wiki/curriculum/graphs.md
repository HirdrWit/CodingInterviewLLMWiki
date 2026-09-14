---
type: curriculum
status: unseen
updated: 2026-09-14
sources: [https://neetcode.io/practice/practice/blind75]
tags: [curriculum, graphs]
---

# Topic 11 — Graphs

**Lesson:** [[../lessons/11-graphs|Lesson 11 — Graphs]]
**Skill:** [[../skills/data-structures|Data Structures]]

6 problems, all Mediums. Worked in this order. The first one establishes that a
grid is a graph; the last one strips the grid away and shows it was the same
problem. Everything in between adds exactly one idea: building as you traverse,
searching backwards from the boundary, and the directed-graph world where cycles
mean something different.

| # | Problem | Difficulty | Pattern | Attempts | Status | Write-up |
|---|---|---|---|---|---|---|
| 1 | Number of Islands | Medium | grid as implicit graph, flood fill | 0 | `unseen` | — |
| 2 | Clone Graph | Medium | DFS with an old-node → new-node map | 0 | `unseen` | — |
| 3 | Pacific Atlantic Water Flow | Medium | multi-source DFS inward from the borders | 0 | `unseen` | — |
| 4 | Course Schedule | Medium | cycle detection / topological sort | 0 | `unseen` | — |
| 5 | Graph Valid Tree | Medium | connectivity + edge count, or union-find | 0 | `unseen` | — |
| 6 | Number of Connected Components in an Undirected Graph | Medium | component counting | 0 | `unseen` | — |

The **Pattern** column is filled in here for tracking, but read it *after* a
timed attempt, never before. Knowing "this is cycle detection" going in removes
the exact skill the interview tests: recognising it yourself.

Problems 5 and 6 are LeetCode Premium-locked; both are freely available on
[neetcode.io](https://neetcode.io/practice/practice/blind75).

## Topic done when

- [ ] All 6 solved at least once
- [ ] All 6 re-solved **cold** (no notes, no hints) on a later day
- [ ] Adjacency list built from an edge list from memory, directed and undirected
- [ ] DFS written both recursively and iteratively, cold
- [ ] Can state the DFS-vs-BFS decision rule and *why* BFS gives shortest paths
- [ ] Can explain why directed cycle detection needs three states and undirected needs two
- [ ] Kahn's algorithm explained aloud, including how it detects a cycle
- [ ] Complexity stated as O(V + E) — or O(R·C) for a grid — without prompting

## Notes

*Accumulates as problems get worked — recurring mistakes, insights that transfer.*
