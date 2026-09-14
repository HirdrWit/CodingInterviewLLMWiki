---
type: lesson
topic: advanced-graphs
status: ready
updated: 2026-09-14
sources: [https://www.geeksforgeeks.org/topological-sorting/, https://www.geeksforgeeks.org/detect-cycle-in-a-graph/, https://www.geeksforgeeks.org/dijkstras-shortest-path-algorithm-greedy-algo-7/, https://www.geeksforgeeks.org/introduction-to-disjoint-set-data-structure-or-union-find-algorithm/, https://www.geeksforgeeks.org/kruskals-minimum-spanning-tree-algorithm-greedy-algo-2/, https://www.geeksforgeeks.org/prims-minimum-spanning-tree-mst-greedy-algo-5/]
tags: [lesson, advanced-graphs]
---

# Lesson 12 — Advanced Graphs

**Curriculum:** [[../curriculum/advanced-graphs|Topic 12]] · **Skill:** [[../skills/algorithms|Algorithms]] · **Materials:** [[../meta/resources|Resources]]
**Prerequisites:** [[11-graphs|Lesson 11 — Graphs]] (BFS, DFS, adjacency lists, topological sort) and [[08-heap|Lesson 8 — Heap]].
**Work through in:** 90 minutes. One problem in the Blind 75, three named
algorithms that aren't — and the named algorithms are the half interviewers
actually ask about.

---

## 1. Why this topic

The obvious reason: this is where the algorithms with surnames live — Dijkstra,
Kruskal, Prim, union-find. They show up in real onsites constantly, even though
the Blind 75 gives them almost no shelf space.

The real reason, and the one this lesson is built around: **Topic 11 handed you
a graph. Topic 12 doesn't.** Every problem in Graphs arrived with the graph
already visible — a grid of cells, a list of edges, a `numCourses` and a
prerequisites array. The traversal was the work.

Advanced Graphs inverts that. The input is a list of words, or a set of exchange
rates, or a pile of "A happened before B" facts, and **there is no graph in the
input at all**. You have to notice that one is implied, decide what the nodes
are, decide what the edges are, build it, and only then run the same BFS or DFS
or topological sort you already know from Lesson 11.

That first half — the modelling — is where these problems are won and lost. The
algorithm is almost always something you already have. So this lesson spends
most of its weight on **recognising a graph in something that isn't shaped like
one**, and the rest on the named algorithms, flagged clearly as beyond the
Blind 75.

---

## 2. The mental model

### Constraints are edges

Hold this sentence in your head for the whole topic:

> **"A must come before B" is a directed edge A → B.**

That's it. That's the modelling move. Any time a problem states a *relative*
fact about two things — comes before, depends on, is cheaper than, must be
installed after, beats in a match — it has just handed you an edge, in prose
instead of in a list.

### The physical analogy: the seating chart

You are seating twelve people at a long table, and instead of a seating plan
you get a stack of index cards from the host:

```
   ┌─────────────────────┐
   │ Ana before Ben      │
   ├─────────────────────┤
   │ Ben before Cara     │
   ├─────────────────────┤
   │ Ana before Dai      │
   ├─────────────────────┤
   │ Dai before Cara     │
   └─────────────────────┘
```

Nobody gave you an order. Nobody gave you a graph. But every card is an arrow,
and once you draw the arrows you have one:

```
        Ana
       ╱   ╲
      ▼     ▼
     Ben   Dai
       ╲   ╱
        ▼ ▼
        Cara
```

Now the questions answer themselves:

- **"Give me a valid order."** → topological sort. `Ana, Ben, Dai, Cara` works;
  so does `Ana, Dai, Ben, Cara`. Multiple valid answers is normal and is a
  strong tell that you're in topo-sort territory.
- **"Is the host's list even satisfiable?"** → is there a cycle? A card saying
  `Cara before Ana` would close the loop and make the whole thing impossible.
- **"Is the order unique?"** → at every step of the sort, was there exactly one
  person with no remaining constraints on them? If ever there were two, the
  answer isn't unique.

Three completely different-sounding questions, one graph, one traversal.

### The three questions to ask of any "implicit graph" problem

1. **What is a node?** Usually the smallest distinct thing being ordered or
   related — a character, a course, a currency, a person. Not the input items
   themselves. In Alien Dictionary the input is *words*, and the nodes are
   emphatically not words.
2. **What is an edge, and which way does it point?** Direction is not a detail.
   Getting it backwards produces a perfectly valid topological order of the
   reversed graph, which is a wrong answer that looks right.
3. **What am I being asked to do on it?** Order → topological sort. Reachability
   → DFS/BFS. Cheapest path with weights → Dijkstra. Connectivity under merging
   → union-find. Cheapest way to connect everything → MST.

Do those three out loud before writing a line. See [[../concepts/implicit-graph]].

---

## 3. The mechanics

### 3.1 Topological sort, and why there are two of them

A **topological order** is a linear ordering of a DAG's nodes such that every
edge points forwards in the list. It exists **if and only if** the graph is
acyclic — which is why "detect a cycle" and "produce an order" are the same
computation wearing two hats. The full treatment is in
[[11-graphs|Lesson 11]] and [[../concepts/topological-sort]]; what matters here
is the choice between the two implementations, because the interviewer will
ask why you picked yours.

**Kahn's algorithm (BFS flavour).** Count how many edges point *into* each node
(its **in-degree**). Anything with in-degree 0 has nothing blocking it — put it
in a queue. Pop one, append it to the output, and decrement the in-degree of
everything it points at; anything that hits 0 joins the queue.

```
in-degree:  Ana 0   Ben 1   Dai 1   Cara 2

queue [Ana]            out: []
 pop Ana  → Ben 0, Dai 0
queue [Ben, Dai]       out: [Ana]
 pop Ben  → Cara 1
queue [Dai]            out: [Ana, Ben]
 pop Dai  → Cara 0
queue [Cara]           out: [Ana, Ben, Dai]
 pop Cara
queue []               out: [Ana, Ben, Dai, Cara]
```

The cycle check falls out for free: **if the output has fewer nodes than the
graph, the leftovers are in a cycle.** Nothing in a cycle ever reaches
in-degree 0, so nothing in a cycle ever enters the queue.

**DFS with colours.** Run DFS; when a node's recursion *finishes* (all its
descendants done), push it onto a stack. Reverse the stack at the end. Cycle
detection needs the colouring below.

**Which to use.** Kahn's, by default, for three reasons: the cycle check is
free and obvious, there's no recursion limit to worry about (Python's default
is 1000 frames, and a path-shaped graph of 10⁵ nodes will blow it), and the
partially-built output is easy to reason about out loud. Reach for DFS when you
want the reverse-postorder property for something else — strongly connected
components, for instance.

### 3.2 Cycle detection in a *directed* graph, and the two-state trap

This is the single most commonly botched thing in the topic, so slow down here.

In an **undirected** graph, a plain `visited` set is enough: if DFS reaches an
already-visited node that isn't the one you came from, there's a cycle.

In a **directed** graph, a `visited` set is **not enough**, and the reason is
the diamond:

```
        A
       ╱ ╲
      ▼   ▼
     B     C
      ╲   ╱
       ▼ ▼
        D
```

DFS from A goes A → B → D, marking all three visited. It returns, goes
A → C → D, and hits D already visited. A two-state scheme says "already seen —
must be a cycle." It isn't. There is no cycle here; D simply has two parents.
This is a **cross edge**, and it's harmless.

The fix is three states, conventionally called **white / grey / black**:

| Colour | Meaning |
|---|---|
| **white** | not visited yet |
| **grey** | on the current DFS stack — we entered it, we haven't left it |
| **black** | fully finished, all descendants explored |

```
     entering                leaving
white ────────► grey ────────────► black
                 │
                 └── an edge INTO a grey node = a back edge = a CYCLE
```

The rule in one line: **an edge to a grey node is a cycle; an edge to a black
node is fine.** Grey means "this node is an ancestor of where I am standing" —
reaching it again means I have walked in a circle. Black means "I already
explored all of that, and found no cycle through it," so there is nothing to do.

The diamond: when the second visit reaches D, D is *black*, not grey. No false
positive. See [[../concepts/cycle-detection-dfs-colors]].

### 3.3 Where Alien Dictionary's difficulty actually is

Without spoiling the derivation: the traversal in that problem is a textbook
topological sort you already know how to write. The Hard rating comes entirely
from the modelling — deciding what the nodes are, working out exactly what
information a pair of words does and does not give you, and handling the one
input shape that makes the whole thing **invalid** rather than merely
unordered. That last one is not a cycle, and it is not an edge case you will
think of at minute 28. Think about invalid inputs at minute 2.

---

## 4. Complexity

`V` = number of nodes, `E` = number of edges. For graphs built from an implicit
input, add the cost of the build — it's often the dominant term, and stating it
separately is a good look.

| Operation | Time | Space | Why |
|---|---|---|---|
| Build adjacency list from constraints | O(input size) | O(V + E) | one pass over the raw input |
| Topological sort — Kahn's | O(V + E) | O(V + E) | each node queued once, each edge relaxed once |
| Topological sort — DFS | O(V + E) | O(V + E) | plus O(V) recursion stack |
| Cycle detection, directed (colours) | O(V + E) | O(V) | same DFS, one colour array |
| Dijkstra with a binary heap | O(E log V) | O(V + E) | each edge can push once; each pop is log V |
| Union-find op (path compression + rank) | ~O(1) amortized | O(V) | inverse-Ackermann, α(n) ≤ 4 in practice |
| Kruskal's MST | O(E log E) | O(V + E) | dominated by sorting the edges |
| Prim's MST with a heap | O(E log V) | O(V + E) | same shape as Dijkstra |

Note the two rows that look identical: **Dijkstra and Prim are the same
algorithm with a different key.** Dijkstra's heap holds *distance from the
source*; Prim's holds *cost of the single edge that attaches this node to the
tree so far*. That observation is worth saying out loud — it shows you see the
structure rather than having memorised two procedures.

---

## 5. The patterns

Each is **a trigger in the problem statement → the tool**. The trigger is the
transferable half.

### Pattern A — constraints are edges → [[../concepts/implicit-graph]]
> **Trigger:** the input is not a graph, but it contains *relative* facts — "X before Y", "X depends on Y", "X is 3× Y", "X beats Y"

Stop and do the three questions from §2: what's a node, what's an edge and which
way, what's the operation. Build the adjacency list explicitly as a separate
step — resist fusing the build into the traversal, because a fused version is
far harder to debug when the direction turns out to be backwards.

The giveaway that you're in this pattern and not a plain array problem: the
answer depends on *transitive* consequences of the facts, not on the facts
individually. If A→B and B→C forces something about A and C, it's a graph.

### Pattern B — "give me an order" → topological sort
> **Trigger:** "return a valid ordering", "in what order can these be done", "is this schedule possible", *and* multiple answers would be acceptable

Kahn's by default. Two sub-flavours worth separating:

- **"Is it possible?"** — you only need to know whether a full topological
  order exists. Count nodes emitted; if it's short, there's a cycle.
- **"Give me the order."** — same run, but you also need the output list, and
  you need to have thought about what "no valid order" returns (usually `""`
  or `[]`, not an exception).

### Pattern C — "detect an impossible constraint set" → colours, or Kahn's count
> **Trigger:** "can all tasks be finished", "is there a contradiction", "return empty if invalid"

Directed graph → three colours, or Kahn's node count. Undirected graph → a plain
visited set plus the parent check, or union-find (if you're about to union two
nodes that are already in the same set, you've found a cycle).
See [[../concepts/cycle-detection-dfs-colors]].

### Pattern D — "cheapest path with unequal costs" → Dijkstra
> **Trigger:** "minimum cost", "shortest time", "cheapest flight", **and the edges have different weights**

The word *weights* is load-bearing. On an unweighted graph — or one where every
edge costs the same — **plain BFS already gives the shortest path**, and reaching
for Dijkstra there is a small red flag. Dijkstra exists precisely because BFS's
"first time I reach a node is the cheapest" guarantee breaks when a three-hop
route can be cheaper than a one-hop route.

### Pattern E — "are these two connected, as things keep getting joined?" → union-find
> **Trigger:** "number of connected components", "redundant connection", "accounts merge", and especially edges arriving **one at a time**

If the graph is static, DFS/BFS over components is simpler and you should say so.
Union-find earns its keep when connectivity changes as you go and you'd otherwise
re-run a traversal after every single edge.

### Pattern F — "connect everything as cheaply as possible" → MST
> **Trigger:** "connect all points at minimum cost", "minimum cost to supply every house with water"

*Connect all of them*, not *get from one to another*. That's the word that
separates MST from Dijkstra, and mixing the two up is the classic error here.

---

## 6. Beyond the Blind 75 — the named algorithms

**Flagged deliberately: none of the following is required for the single Blind 75
problem in this topic.** They're here because interviewers ask about them far
out of proportion to their weight in the list. The bar for now is *recognise the
trigger, state the complexity, sketch the idea*. Full implementations come later,
if a target company's tags justify it.

### Dijkstra — cheapest path, non-negative weights

Keep a `dist` map, all infinity except the source at 0. Repeatedly take the
**unfinished node with the smallest known distance**, finalise it, and relax its
outgoing edges (`if dist[u] + w < dist[v]: dist[v] = dist[u] + w`).

**Why it needs a heap.** "Take the unfinished node with the smallest distance"
is a min-priority query, executed once per node. Scanning an array for it is
O(V) per step and O(V²) overall — fine for dense graphs, bad for sparse ones.
A binary heap answers it in O(log V), giving O(E log V). This is exactly the
machine from [[08-heap|Lesson 8]], and Dijkstra is the best answer to "when
would you actually reach for a heap".

```python
import heapq

def dijkstra(adj, src):                   # adj: {u: [(v, weight), ...]}
    dist = {src: 0}
    pq = [(0, src)]                       # (distance_so_far, node)
    while pq:
        d, u = heapq.heappop(pq)
        if d > dist.get(u, float("inf")):
            continue                      # stale entry — already finalised better
        for v, w in adj.get(u, []):
            nd = d + w
            if nd < dist.get(v, float("inf")):
                dist[v] = nd
                heapq.heappush(pq, (nd, v))
    return dist
```

Two things to notice, both of which get asked about. The **lazy deletion** —
Python's `heapq` has no decrease-key, so you push a new entry and skip stale
pops with that `continue`. And the **non-negative requirement**: with a negative
edge, a node you already finalised could later become cheaper, and Dijkstra
never revisits it. Negative weights need Bellman-Ford, O(V·E).

### Union-find (disjoint set union)

Each set is a tree; every node points at a parent, and the root is the set's
identity. `find(x)` walks to the root. `union(a, b)` points one root at the other.

Two optimisations, and you should name both:

- **Path compression** — during `find`, re-point every node on the path directly
  at the root, so the next `find` is a single hop.
- **Union by rank (or size)** — always hang the shorter tree under the taller
  one, so trees stay shallow.

```python
class DSU:
    def __init__(self, n):
        self.parent = list(range(n))
        self.rank = [0] * n

    def find(self, x):
        while self.parent[x] != x:
            self.parent[x] = self.parent[self.parent[x]]   # path compression
            x = self.parent[x]
        return x

    def union(self, a, b):
        ra, rb = self.find(a), self.find(b)
        if ra == rb:
            return False                                   # already joined — a cycle
        if self.rank[ra] < self.rank[rb]:
            ra, rb = rb, ra
        self.parent[rb] = ra
        if self.rank[ra] == self.rank[rb]:
            self.rank[ra] += 1
        return True
```

Together the two give **amortized near-O(1)** per operation — formally O(α(n)),
the inverse Ackermann function, which is at most 4 for any input that fits in
the universe. Saying "effectively constant, formally inverse Ackermann" is the
answer they're listening for. Without either optimisation it degrades to O(n)
per operation.

### Minimum spanning tree — Kruskal and Prim

An MST is the cheapest set of edges that keeps every node connected: exactly
`V − 1` edges, no cycles.

- **Kruskal** — sort all edges by weight, then add them cheapest-first, skipping
  any edge whose endpoints are already connected. That "already connected?" test
  is union-find, which is why the two topics always appear together.
  **O(E log E)**, dominated by the sort.
- **Prim** — grow one tree from an arbitrary start, repeatedly adding the
  cheapest edge that leaves the tree. Heap-driven, **O(E log V)** — structurally
  identical to Dijkstra with a different heap key.

Kruskal when the edge list is given and the graph is sparse; Prim when the graph
is dense or you're generating edges on the fly (e.g. points on a plane where
every pair is implicitly an edge).

---

## 7. Traps

- **A two-state `visited` set for a directed cycle check.** The diamond in §3.2
  makes it report a cycle that isn't there. Three colours, or Kahn's count.
- **Edge direction backwards.** Produces a clean, valid, wrong answer — the
  worst failure mode there is, because nothing crashes. Write down one concrete
  edge from the example *before* coding, and re-derive the direction from it.
- **Not initialising every node.** Nodes that appear in the input but have no
  constraints on them still belong in the output. If you build the adjacency
  list only from the constraint pairs, isolated nodes silently vanish and your
  output is short. Seed the node set from the raw input first, edges second.
- **Confusing "no valid order" with "empty input".** Both often return `""` or
  `[]`. Decide what each means and handle them separately.
- **Treating "multiple valid answers" as a bug.** Topological orders aren't
  unique. If the problem demands a specific one (lexicographically smallest,
  say), that's Kahn's with a heap instead of a queue — say so rather than
  hoping.
- **Dijkstra with negative weights.** It is simply wrong, not slow. Say
  "Dijkstra assumes non-negative edges" unprompted; it's a standard follow-up.
- **Dijkstra where BFS would do.** Unweighted or uniform weights → BFS. Using
  the heavier tool suggests pattern-matching on "shortest path" rather than
  reading the problem.
- **Python's recursion limit.** DFS on 10⁵ nodes in a path shape overflows at
  ~1000 frames. Iterative, or Kahn's.
- **Reporting O(V + E) while having built the graph in O(V²).** If you compared
  every pair of inputs to find the edges, that's in the complexity too.

---

## 8. Worked example

Outside the Blind 75, so nothing is spoiled. This one is pure Pattern A — the
modelling move with a traversal you already own.

> **Problem** (LeetCode 399, *Evaluate Division*). You're given equations like
> `a / b = 2.0` and `b / c = 3.0`, and asked queries like `a / c`. Return the
> value, or `-1.0` if it can't be determined.
> `equations = [["a","b"],["b","c"]]`, `values = [2.0, 3.0]`,
> `queries = [["a","c"],["a","e"]]` → `[6.0, -1.0]`.

**First reaction.** It looks like arithmetic. There's no graph in the input —
it's two parallel arrays and a list of pairs. That's the tell for this topic:
*the graph is implied, not given.*

**The three questions.**

1. **What's a node?** A variable name: `a`, `b`, `c`. Not an equation.
2. **What's an edge?** `a / b = 2.0` says two things at once — an edge
   `a → b` with weight `2.0`, **and** an edge `b → a` with weight `1/2.0`.
   Writing only one direction is the mistake to avoid; the relation is
   invertible and the graph must say so.
3. **What's the operation?** A query `a / c` is "find any path from `a` to `c`
   and multiply the weights along it". That's a reachability walk — plain DFS.

```
        2.0        3.0
   a ────────► b ────────► c
     ◄──────    ◄──────
       0.5        1/3

   query a/c:  follow a→b→c,  2.0 × 3.0 = 6.0
   query a/e:  'e' is not a node at all → -1.0
```

**The key realisation:** the answer along a path is the *product* of the edge
weights, because division chains multiply. If the weights had been additive
costs, the same graph with a sum instead of a product would answer it. The
graph structure is independent of what you accumulate along it.

```python
from collections import defaultdict

def build(equations, values):
    adj = defaultdict(dict)
    for (num, den), val in zip(equations, values):
        adj[num][den] = val
        adj[den][num] = 1.0 / val        # the inverse edge — easy to forget
    return adj

def query(adj, src, dst):
    if src not in adj or dst not in adj:
        return -1.0                      # unknown variable, not "unconnected"
    stack, seen = [(src, 1.0)], {src}
    while stack:
        node, acc = stack.pop()
        if node == dst:
            return acc
        for nxt, w in adj[node].items():
            if nxt not in seen:
                seen.add(nxt)
                stack.append((nxt, acc * w))
    return -1.0                          # different components
```

**Complexity.** Build is O(E). Each query is O(V + E) worst case. Total
O(E + Q·(V + E)).

**The move to internalise.** The entire difficulty was in the first thirty
seconds: *these equations are edges*. Once that landed, the code was a DFS with
an accumulator — something Lesson 11 already gave you. That ratio, almost all
modelling and almost no algorithm, is what this whole topic is.

Note too the two different `-1.0` cases: an unknown variable, and a known
variable in a different component. Different reasons, same output, and an
interviewer may well ask you to distinguish them.

---

## 9. Materials

Full list on [[../meta/resources|Resources]]. In order:

1. Re-read [[11-graphs|Lesson 11]] §§ on adjacency lists and topological sort
   before anything else here. This lesson assumes all of it.
2. [Topological sorting](https://www.geeksforgeeks.org/topological-sorting/) —
   both implementations side by side
3. [Detect cycle in a graph](https://www.geeksforgeeks.org/detect-cycle-in-a-graph/) —
   the directed case and why the colouring is needed
4. [Dijkstra's shortest path](https://www.geeksforgeeks.org/dijkstras-shortest-path-algorithm-greedy-algo-7/)
5. [Disjoint set / union-find](https://www.geeksforgeeks.org/introduction-to-disjoint-set-data-structure-or-union-find-algorithm/) —
   read the path-compression and union-by-rank sections properly
6. [Kruskal's MST](https://www.geeksforgeeks.org/kruskals-minimum-spanning-tree-algorithm-greedy-algo-2/)
   and [Prim's MST](https://www.geeksforgeeks.org/prims-minimum-spanning-tree-mst-greedy-algo-5/) —
   skim both, then answer "how are these different" aloud
7. [NeetCode on YouTube](https://www.youtube.com/@NeetCode) — search the problem
   name, but **only after** a timed attempt and a debrief
8. Optional, for Dijkstra reps: search LeetCode for *Network Delay Time* and
   *Cheapest Flights Within K Stops*

---

## 10. Self-check

Aloud, full sentences, before starting the timer.

1. "A must come before B" — what is that, structurally? What are the nodes?
2. Why is a two-element `visited`/`not visited` scheme insufficient for cycle
   detection in a **directed** graph? Draw the counterexample.
3. What do white, grey and black mean, and which colour on the far end of an
   edge means you've found a cycle?
4. How does Kahn's algorithm detect a cycle without any extra bookkeeping?
5. When would you choose DFS-based topological sort over Kahn's?
6. Why does Dijkstra need a heap? What's the complexity with one and without?
7. Why does Dijkstra break on negative edge weights — and what do you use instead?
8. What are the two optimisations in union-find, and what's the amortized cost
   with both?
9. What single word in a problem statement separates an MST problem from a
   shortest-path problem?
10. Give the trigger phrase for each of patterns A–F.

---

## 11. Ready?

**First timed problem: Alien Dictionary** (Hard) — Patterns A, B and C together.

Say `timed alien-dictionary` and I'll give you the statement and start the
30-minute box. No hints while it's running.

This is a Hard, and it's the first one where the algorithm is the easy half.
**Not finishing is the expected outcome, and it is not a failure** — it's data
about where the modelling ceiling currently is.

**What "good" looks like here** — deliberately not "solved":

- You spend the first **five minutes not coding**. Out loud: what's a node,
  what's an edge, which direction, what operation.
- You write one concrete edge from the worked example by hand before touching
  the keyboard, and you can justify its direction.
- You seed the node set from the whole input, not just from the edges you found
- You ask yourself what makes an input *invalid* rather than merely unordered —
  at minute 2, not minute 28
- You name the traversal you're about to use, and why that one
- You state complexity in two parts: the build and the sort

If the 30 minutes run out with a half-built graph and no sort, that is a
perfectly good outcome for attempt one. It goes in the
[[../meta/review-queue|review queue]] at +2 days and comes back.
**Repeat until cold-solvable is the plan, not the fallback.**
