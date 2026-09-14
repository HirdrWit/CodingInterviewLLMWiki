---
type: concept
status: unseen
updated: 2026-09-14
sources: []
tags: [advanced-graphs, graphs]
---

# Implicit Graph

**In one sentence, in my own words** — no textbook phrasing: when a problem
hands me a pile of *relative* facts ("A before B", "a/b = 2", "X beats Y")
rather than a graph, the facts **are** the edges, and my first job is to decide
what a node is and which way each arrow points before running any traversal.

Pattern A in [[../lessons/12-advanced-graphs|Lesson 12]]. The difference between
Topic 11 and Topic 12 in one idea: Graphs gives you the graph, Advanced Graphs
makes you find it.

## When to reach for it

Trigger: the input is a list of words, equations, pairs, rankings, or rules —
and it contains statements about how two things relate to *each other*, not
about either one alone.

The sharper test: **does the answer depend on transitive consequences?** If
knowing A→B and B→C forces something about A and C, it's a graph. If each fact
can be handled independently, it isn't — it's a loop over the input.

Phrases that show up: "must come before", "depends on", "is equivalent to",
"is N times", "these are sorted according to some unknown rule".

## How it works

Three questions, answered aloud, before any code:

1. **What is a node?** Almost always the smallest distinct atom being related —
   a character, a variable, a course, a person. It is usually *not* the input
   items themselves. Input of words → nodes are probably not words.
2. **What is an edge, and which way does it point?** Direction is the whole
   ballgame. Reversing it yields a valid answer to the wrong question, and
   nothing crashes to tell you.
3. **What operation runs on it?** Order → topological sort. Reachability →
   DFS/BFS. Weighted cheapest path → Dijkstra. Connectivity under merging →
   union-find.

Then build the adjacency list as its **own step**, separate from the traversal.
A fused build-and-walk is much harder to debug when the direction is wrong.

## Minimal example

```
input:  ["Ana before Ben", "Ben before Cara", "Ana before Dai", "Dai before Cara"]

nodes:  {Ana, Ben, Cara, Dai}          ← seeded from ALL names, not just edges
edges:  Ana→Ben, Ben→Cara, Ana→Dai, Dai→Cara

           Ana
          ╱   ╲
         ▼     ▼
        Ben   Dai
          ╲   ╱
           ▼ ▼
           Cara

operation: topological sort → [Ana, Ben, Dai, Cara]  (or [Ana, Dai, Ben, Cara])
```

## Complexity

Building: **O(size of the raw input)** — one pass, if the edges are stated
directly. If you have to *compare pairs* of inputs to discover edges, it's
O(n²) or worse, and that term goes in the final complexity.

Traversal afterwards: whatever the chosen algorithm costs, in terms of the V and
E of the graph *you built* — which are not the same numbers as the input size.
State the two parts separately; conflating them is a common way to under-report.

Space: O(V + E) for the adjacency list.

## Gotchas

- **Isolated nodes disappear.** If the node set is built only from the edge
  pairs, anything mentioned in the input with no constraints on it silently
  drops out of the output. Seed nodes from the raw input first, edges second.
- **Edge direction backwards.** Silent, plausible, wrong. Derive one concrete
  edge by hand from the worked example and check it before coding.
- **Symmetric relations need both directions.** `a / b = 2` is *two* edges,
  `a→b` weight 2 and `b→a` weight 0.5.
- **V and E are not the input size.** Complexity is in terms of the built graph.
- **Not every relation is a graph.** If nothing transitive is being asked, a
  graph is over-engineering — say so and move on.

## Related

- [[topological-sort]] — by far the most common operation to run on the graph
  once it's built; owned by [[../lessons/11-graphs|Lesson 11]]
- [[cycle-detection-dfs-colors]] — the check for whether the constraints you
  modelled are even satisfiable
- [[hash-map]] — the adjacency list is a map from node to neighbours, so the
  build is a hash-map problem wearing a graph costume

## Evidence

Problems where I used this unaided:
