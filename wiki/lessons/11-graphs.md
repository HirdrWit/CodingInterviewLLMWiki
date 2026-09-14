---
type: lesson
topic: graphs
status: ready
updated: 2026-09-14
sources: [https://www.geeksforgeeks.org/graph-data-structure-and-algorithms/, https://www.geeksforgeeks.org/graph-and-its-representations/, https://www.geeksforgeeks.org/topological-sorting/]
tags: [lesson, graphs]
---

# Lesson 11 — Graphs

**Curriculum:** [[../curriculum/graphs|Topic 11]] · **Materials:** [[../meta/resources|Resources]]
**Prerequisites:** [[07-trees|Lesson 7 — Trees]] (DFS/BFS on a tree), and the
seen-set habit from [[01-arrays-hashing|Lesson 1]].
**Work through in:** 90 minutes plus videos. This is the topic where the
previous ten start paying compound interest.

---

## 1. Why this topic

The obvious reason: graph questions are everywhere at the FAANG-adjacent tier,
and "model this as a graph" is a whole category of interview answer.

The real reason is smaller and more useful: **graphs are not a new traversal.
They are the traversal you already know from Topic 7, with exactly one thing
added — a visited set.** A tree is a graph with two restrictions that let you be
careless: it can't loop back on itself, and every node has exactly one parent.
Take those away and your tree DFS is still correct in shape; it just runs
forever unless you remember where you've been.

That's the whole conceptual delta of this topic, and it means most of the work
here is recognition, not new machinery. The skill being tested is almost never
"can you write DFS". It's **"can you see that this problem — a grid of land and
water, a list of course prerequisites, a set of node copies — is a graph?"**
Nothing in those three statements says the word "graph". That translation step
is the interview.

The other reason this topic matters disproportionately: it's the first time the
*choice* between DFS and BFS is load-bearing. Up to now they were interchangeable
ways to touch every node. Here, one of them answers "shortest path" and the
other doesn't.

---

## 2. The mental model

### A graph is a map of cities and roads

Forget boxes in memory. A graph is **things** (nodes/vertices, `V`) and
**relationships between them** (edges, `E`). Cities and roads. People and
friendships. Courses and prerequisites. Web pages and links.

```
        A ────── B
        │      ╱ │
        │    ╱   │
        │  ╱     │
        C ────── D        E ─── F
```

Two things to notice immediately, because both are illegal in a tree:

- **There's a cycle.** A → B → C → A gets you home. Walk it without bookkeeping
  and you loop until the stack blows.
- **It's disconnected.** E–F is a second island. A traversal starting at A never
  reaches E. So "visit the whole graph" means *loop over every node and start a
  fresh traversal from any you haven't seen* — not one call from a root. In fact
  there **is** no root. That's the other tree luxury you lose.

### The one thing graphs add

Put the tree version and the graph version side by side:

```
   TREE DFS                        GRAPH DFS
   def dfs(node):                  def dfs(node):
       if not node: return             if node in visited: return   ← the delta
                                       visited.add(node)
       visit(node)                     visit(node)
       dfs(node.left)                  for nxt in adj[node]:
       dfs(node.right)                     dfs(nxt)
```

Structurally identical. The base case changed from "ran out of tree" to "been
here already", and the two fixed children became a loop over a neighbour list.
If you can write the left one cold — and after Topic 7 you can — you can write
the right one.

> **The sentence to carry out of this lesson:** a tree is a graph that can't
> loop back, and the one thing graphs add is the [[../concepts/visited-set|visited set]].

---

## 3. The mechanics — enough to reason about cost

### The three representations → [[../concepts/graph-representation|concept page]]

A graph is abstract. Before you can traverse one you have to decide how it's
stored, and interviewers ask this directly.

**1. Adjacency list** — a map from each node to a list of its neighbours.

```
adj = {A: [B, C], B: [A, C, D], C: [A, B, D], D: [B, C]}
```

Space O(V + E). Finding a node's neighbours is O(1) to reach the list, then O(k)
to walk its k neighbours. **This is the default. Assume it unless told
otherwise.** Real graphs are sparse — most pairs of things are not connected —
and the list only pays for edges that exist.

**2. Adjacency matrix** — a V×V grid where `m[i][j] = 1` means an edge exists.

```
      A  B  C  D
   A [ 0  1  1  0 ]
   B [ 1  0  1  1 ]
   C [ 1  1  0  1 ]
   D [ 0  1  1  0 ]
```

Space O(V²) no matter how few edges there are. Its one superpower: **"is there
an edge from i to j?" is O(1)**, whereas the adjacency list has to scan a
neighbour list. Right when the graph is dense (E approaches V²), when you need
that edge test constantly, or when V is small and fixed.

**3. Edge list** — just `[(A,B), (B,C), (C,A), ...]`.

Compact, and it's usually the *input format the problem hands you*. Useless for
traversal on its own: answering "what are A's neighbours?" means scanning every
edge, O(E). So the first line of a huge number of graph solutions is converting
it:

```python
from collections import defaultdict

adj = defaultdict(list)
for u, v in edges:
    adj[u].append(v)
    adj[v].append(u)     # ← this line ONLY for undirected graphs
```

Build that in your sleep. Note the trap already: `defaultdict(list)` silently
returns `[]` for a node with no edges, which is what you want — but it also
means isolated nodes never appear as keys. If the problem says "n nodes labelled
0..n-1", iterate `range(n)`, not `adj.keys()`.

### The four axes a graph varies on

Every graph problem, before you write anything, answer these:

| Axis | Options | What it changes |
|---|---|---|
| Direction | directed / undirected | Whether you add the reverse edge. Whether a cycle means what you think. |
| Weights | weighted / unweighted | Unweighted shortest path = BFS. Weighted = Dijkstra (Topic 12, not yet). |
| Connectivity | connected / disconnected | Whether one traversal suffices, or you loop over all starts. |
| Cycles | may contain / guaranteed acyclic | A directed acyclic graph (DAG) is what topological sort needs. |

Get these wrong and the algorithm is wrong before it starts. "Is this graph
directed?" is a legitimate clarifying question and asking it scores points.

### Why O(V + E)

Both DFS and BFS, with a visited set, touch **each node once** (the set blocks
the second visit) and **each edge at most twice** (once from each endpoint, in
an undirected graph). So the total work is proportional to V + E — not V × E,
and not V². You can't write "O(n)" here because there are two sizes; saying
"O(V + E)" unprompted is a small, cheap signal that you actually know the model.

For a grid of R rows and C columns treated as a graph, V = R·C and each cell has
at most 4 edges, so E = O(R·C) and the whole thing collapses to **O(R·C)** —
linear in the number of cells.

---

## 4. Complexity

Adjacency list assumed except where stated.

| Operation | Cost | Why |
|---|---|---|
| Build adj list from E edges | O(V + E) | one append per edge |
| DFS / BFS full traversal | **O(V + E)** | each node once, each edge ≤ twice |
| DFS / BFS space | O(V) | visited set, plus stack/queue up to V |
| "Is there an edge u→v?" — adj **list** | O(degree(u)) | scan u's neighbours |
| "Is there an edge u→v?" — adj **matrix** | **O(1)** | direct index |
| Storage — adj list | O(V + E) | sparse-friendly |
| Storage — adj matrix | O(V²) | pays for absent edges too |
| Topological sort (Kahn) | O(V + E) | it *is* a BFS |
| Recursive DFS stack depth | O(V) worst case | a path graph recurses V deep |

The last row is a real constraint, not a formality: Python's default recursion
limit is 1000. A 10⁵-node path graph will `RecursionError` on a recursive DFS
that is otherwise perfectly correct. Know the iterative form.

---

## 5. The patterns

Each one is **a trigger in the problem statement → the tool it should summon.**
Learn the triggers. This section is the transferable half of the lesson.

### Pattern A — grid as an implicit graph → [[../concepts/dfs-graph|DFS]]
> **Trigger:** a 2-D grid of cells, and "regions", "islands", "areas", "connected cells", "flood"

There is no adjacency list and there are no edge objects. The graph is *implied*:
**every cell is a node, and its neighbours are the four cells up/down/left/right.**
Recognising that a grid is a graph is the single most common translation step in
this topic.

The idiom, which you should be able to type without thinking:

```python
DIRS = ((-1, 0), (1, 0), (0, -1), (0, 1))     # up, down, left, right

for dr, dc in DIRS:
    nr, nc = r + dr, c + dc
    if 0 <= nr < rows and 0 <= nc < cols and grid[nr][nc] == TARGET:
        ...
```

Bounds check **before** you index. And note what "visited" can be here: a
separate set of `(r, c)` tuples, or — if you're allowed to mutate the input —
overwriting the cell itself, which is O(1) extra space. Always say out loud that
you're mutating the caller's grid; some interviewers care.

### Pattern B — traversal that builds as it goes
> **Trigger:** "clone", "copy", "deep copy" of a linked structure with possible cycles

A DFS that doesn't just *visit* but *constructs*. The visited set upgrades into a
**map from original node → the thing you built for it**, and that map does two
jobs at once: it stops the infinite loop, and it makes sure a node reached twice
by two different paths resolves to the *same* new object rather than two copies.
When the map's lookup answers "have I seen this?" and "what did I make for it?"
simultaneously, you've got the shape right.

This is the [[../concepts/seen-set|seen-set]] from Lesson 1, grown up: a set when
you only care *whether*, a map when you care *what you made*.

### Pattern C — multi-source traversal
> **Trigger:** "reachable from *any* of…", "cells that can reach both X and Y", "spreads from all these at once"

Don't run one traversal per source and intersect — that's O(sources × (V+E)).
Instead, **seed the traversal with every source already marked**, then run one
traversal. For a BFS: push all sources into the queue before the loop starts.
For DFS: call it once per source, into one shared visited set.

The second half of the trick, and the one that's harder to see: sometimes the
question is asked forwards ("which cells drain to the ocean?") but is far cheaper
answered **backwards** ("which cells can the ocean reach, going uphill?"). When a
forward search would need to be re-run from every cell, check whether reversing
the direction of the question turns it into one search from the boundary.

### Pattern D — cycle detection → [[../concepts/topological-sort|topological sort]]
> **Trigger:** "prerequisites", "dependencies", "can you finish/order/schedule", "is it possible to complete"

Dependency language is directed-graph language. "B requires A" is an edge A → B,
and the question "can all of it be done?" is exactly **"is this graph acyclic?"** —
because a cycle is a set of tasks each waiting on the next, forever.

Two directions on this, and they're genuinely different algorithms:

**Undirected cycle detection.** Walk with DFS; if you reach an already-visited
node that **isn't the parent you came from**, there's a cycle. The parent
exclusion is essential — every undirected edge looks like a 2-cycle otherwise.

**Directed cycle detection.** Parent-tracking doesn't work: reaching a visited
node is fine if that node is finished (it's a cross-edge, a diamond, not a loop).
You need **three states**, not two:

```
   white  = never touched
   grey   = on the current recursion path  ← revisiting THIS means a cycle
   black  = fully explored, all descendants done
```

Hitting grey = cycle. Hitting black = already handled, just return. Collapsing
grey and black into one "visited" set is the classic wrong answer, and it reports
cycles in perfectly valid diamond-shaped graphs.

### Pattern E — Kahn's algorithm (topological sort as a BFS)
> **Trigger:** the same dependency language, but asking for **an order**, not just possible/impossible

Count how many prerequisites each node has (its **in-degree**). Everything with
in-degree 0 can start now — queue them. Pop one, output it, and decrement the
in-degree of everything it points at; anything that drops to 0 just became
available, so queue it.

```
   courses: 0 → 1, 0 → 2, 1 → 3, 2 → 3

   in-degree:  0:0  1:1  2:1  3:2
   queue: [0]                    output: []
   pop 0 → 1,2 drop to 0         output: [0]     queue: [1,2]
   pop 1 → 3 drops to 1          output: [0,1]   queue: [2]
   pop 2 → 3 drops to 0          output: [0,1,2] queue: [3]
   pop 3                         output: [0,1,2,3]
```

**And here's the cycle test for free:** if you finish the loop having output
fewer than V nodes, the leftovers are all waiting on each other — a cycle. That
makes Kahn's a single algorithm that answers both "can it be done?" and "in what
order?", which is why it's usually the better answer of the two for Pattern D.

### Pattern F — counting components
> **Trigger:** "how many groups/islands/components", "are they all connected"

One counter, one shared visited set, and an outer loop over every node:

```
count = 0
for node in all_nodes:
    if node not in visited:
        count += 1
        flood(node)        # DFS or BFS — marks the whole component visited
```

The outer loop is the part people forget, and it's exactly the disconnected-graph
consequence from section 2. Note this pattern **is** Pattern A once you notice a
grid's islands are its connected components.

### Pattern G — connectivity + edge count
> **Trigger:** "is this a valid tree", "exactly one path between every pair", "no cycles and all connected"

A graph on V nodes is a tree **iff** it's connected **and** has exactly V − 1
edges. Two conditions, and neither alone is enough: V−1 edges with a cycle
somewhere leaves a fragment disconnected; connected with V edges has a cycle.
Check the cheap one (edge count, O(1)) first, then do one traversal from node 0
and confirm you reached all V nodes.

*(There's a second classic tool for this whole family — union-find / disjoint
set union, which merges nodes into groups as you scan the edges. It's the star of
Topic 12; you don't need it here, and a DFS answer is a complete answer.)*

### The decision rule: DFS or BFS?

| Use | When |
|---|---|
| **[[../concepts/bfs-graph\|BFS]]** | **shortest path / fewest steps in an unweighted graph** — this is the one non-negotiable case. Also "level by level", "spreading outward", "minimum number of moves". |
| **[[../concepts/dfs-graph\|DFS]]** | reachability, connectivity, "does a path exist", counting components, cycle detection, anything about *structure* rather than *distance*. Also: it's shorter to write recursively. |

BFS finds shortest paths because it expands in rings — every node at distance 1
before any node at distance 2 — so the first time it reaches a node, it reached
it by the fewest edges. DFS gives no such guarantee: it dives, and the first path
it happens to find can be arbitrarily long. (This is the
[[../concepts/bfs-level-order|level-order]] machinery from Topic 7, with a
visited set bolted on.)

---

## 6. Python notes

```python
from collections import defaultdict, deque

adj = defaultdict(list)
for u, v in edges:
    adj[u].append(v)
    adj[v].append(u)            # undirected only

# Recursive DFS
def dfs(node):
    if node in visited: return
    visited.add(node)
    for nxt in adj[node]:
        dfs(nxt)

# Iterative DFS — same traversal, no recursion limit
stack = [start]
while stack:
    node = stack.pop()
    if node in visited: continue
    visited.add(node)
    stack.extend(adj[node])

# BFS — note visited is marked ON PUSH, not on pop
q = deque([start])
visited = {start}
while q:
    node = q.popleft()
    for nxt in adj[node]:
        if nxt not in visited:
            visited.add(nxt)
            q.append(nxt)
```

Three Python-specific things that bite:

- **`deque.popleft()` is O(1); `list.pop(0)` is O(n).** Using a list as a queue
  turns an O(V+E) BFS into O(V²). This is the most common accidental blowup in
  the topic.
- **Recursion limit.** `sys.setrecursionlimit(10**6)` exists and interviewers
  accept it, but saying "I'd convert this to an iterative DFS for deep graphs" is
  the better answer.
- **`visited` as a set of tuples** for grids: `visited.add((r, c))`. Lists aren't
  hashable; tuples are.

---

## 7. Traps

- **No visited set.** Infinite loop, or stack overflow. The single defining bug
  of this topic. If you wrote a tree traversal and pasted it onto a graph, this
  is what happens.
- **Marking visited on pop instead of on push in BFS.** A node reachable from
  three neighbours gets queued three times before it's ever popped. Still
  terminates, but the queue bloats and the complexity argument falls apart.
- **Forgetting the outer loop on a disconnected graph.** One DFS from node 0
  answers a question about node 0's component, not the graph. Always ask whether
  the graph is guaranteed connected.
- **Adding the reverse edge on a directed graph** (or forgetting it on an
  undirected one). Both are silent — the code runs and gives a wrong answer.
- **Using two-state visited for directed cycle detection.** Reports cycles on
  valid DAGs. You need white/grey/black.
- **Forgetting parent-exclusion in undirected cycle detection.** Reports a cycle
  on a straight line of two nodes.
- **`list.pop(0)` as a queue.** See above.
- **Indexing before bounds-checking in a grid.** And remember Python's negative
  indices don't raise — `grid[-1][0]` cheerfully wraps to the last row, so a
  missing `0 <= nr` check is a wrong answer rather than a crash.
- **Claiming O(n).** There are two sizes. Say O(V + E), or O(R·C) for a grid.
- **Isolated nodes missing from a `defaultdict`.** Iterate `range(n)`, not the
  dict's keys.

---

## 8. Worked example

Nothing from the problem set, so nothing is spoiled.

> **Problem.** You're given `n` nodes labelled `0` to `n-1` and a list of
> undirected `edges`. Determine whether a path exists between `source` and
> `destination`.
> `n = 6`, `edges = [[0,1],[1,2],[3,4]]`, `source = 0`, `dest = 2` → `True`.
> Same graph, `source = 0`, `dest = 4` → `False`.

**First: classify it.** Undirected. Unweighted. Possibly disconnected (the
example proves it). Question is *reachability*, not distance. Input is an **edge
list**, so step one is a conversion.

**Brute force?** There isn't a meaningfully dumber version — that's a tell that
the problem is testing whether you know the standard machinery, not whether you
can optimise. Say so and move.

**Pick the tool.** Reachability, not shortest path → the decision rule says DFS
(section 5). BFS would also be correct; DFS is shorter and the question doesn't
reward distance.

```python
from collections import defaultdict

def valid_path(n, edges, source, dest):
    adj = defaultdict(list)
    for u, v in edges:
        adj[u].append(v)
        adj[v].append(u)               # undirected: both directions

    visited = set()
    stack = [source]                   # iterative: no recursion depth risk
    while stack:
        node = stack.pop()
        if node == dest:
            return True                # early exit — don't finish the traversal
        if node in visited:
            continue
        visited.add(node)
        stack.extend(adj[node])
    return False
```

**Complexity.** O(V + E) time — the build is O(E), the traversal touches each
node once and each edge twice. O(V + E) space: the adjacency list dominates, the
visited set and stack are O(V).

**Edge cases worth naming aloud:** `source == dest` (returns True immediately —
check that it does), a node with no edges at all (`adj` has no key for it;
`defaultdict` returns `[]`, fine), `n = 1`.

**The move to internalise:** the first three lines of the solution are a
*translation* — edge list into adjacency list — and the rest is Topic 7's DFS
with `visited` added. That's the shape of most graph problems. The thinking is
almost entirely in the modelling, not the algorithm.

---

## 9. Materials

Full list on [[../meta/resources|Resources]]. In order:

1. [Graph data structure and algorithms](https://www.geeksforgeeks.org/graph-data-structure-and-algorithms/) — the hub page; skim the taxonomy
2. [Graph and its representations](https://www.geeksforgeeks.org/graph-and-its-representations/) — 15 min, the section-3 material properly
3. [Breadth First Search](https://www.geeksforgeeks.org/breadth-first-search-or-bfs-for-a-graph/) and [Depth First Search](https://www.geeksforgeeks.org/depth-first-search-or-dfs-for-a-graph/) — do both, back to back, and notice how little differs
4. [Topological sorting](https://www.geeksforgeeks.org/topological-sorting/) — then search specifically for *Kahn's algorithm* and read that; it's the version to know
5. [NeetCode on YouTube](https://www.youtube.com/@NeetCode) — the Graphs playlist, **only after** a timed attempt on the problem in question
6. Optional but genuinely good: search YouTube for *William Fiset graph theory* — a long, careful playlist covering everything here and most of Topic 12

---

## 10. Self-check

Aloud, in full sentences, before the timer starts.

1. What exactly does a graph have that a tree doesn't — and what one line of code does that force you to add?
2. Name the three representations. For each: when is it the right choice?
3. Write the edge-list → adjacency-list conversion from memory. Which line disappears for a directed graph?
4. Why is traversal O(V + E) rather than O(V × E)?
5. When must you use BFS and not DFS? Why does BFS have that property?
6. Why do you need three colours for directed cycle detection, when two suffice for undirected?
7. Describe Kahn's algorithm in four sentences. How does it detect a cycle?
8. A grid of land and water — what are the nodes, and what are the edges?
9. Why is `list.pop(0)` a bug in a BFS?
10. Give the trigger phrase for each of patterns A–G.

---

## 11. Ready?

**First timed problem: Number of Islands** (Medium) — Pattern A.

Say `timed number-of-islands` and I'll give you the statement and start the
30-minute box. No hints while it's running.

**What "good" looks like here:**

- You say "this grid is a graph — each cell is a node, its four neighbours are
  its edges" **before** writing any code. That sentence is the actual answer.
- You decide DFS vs BFS deliberately and say why, rather than defaulting.
- Your visited handling is explicit, and if you mutate the grid instead of
  keeping a set, you say so and give the space trade-off.
- Bounds check comes before the index, every time.
- You state O(R·C) time and are specific about the space — including the worst
  case for the recursion stack, which is not O(1).
- You name the empty grid and the all-water grid before declaring done.

If you finish inside 30 minutes, roll straight into **Number of Connected
Components** — it's the same pattern with the grid removed, and doing them back
to back is what makes the "a grid is just a graph" equivalence stick.

These are all Mediums. **Not finishing is expected**; it goes to the
[[../meta/review-queue|review queue]] at +2 days and comes back. The plan is to
repeat until it's cold-solvable, not to clear the list once.
