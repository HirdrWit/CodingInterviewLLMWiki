---
type: concept
status: unseen
updated: 2026-09-14
sources: [https://www.geeksforgeeks.org/detect-cycle-in-a-graph/]
tags: [advanced-graphs, graphs]
---

# Cycle Detection with DFS Colours

**In one sentence, in my own words** — no textbook phrasing: in a **directed**
graph a plain visited/not-visited flag can't tell "I've looped back onto my own
path" from "I've reached something I already finished exploring", so DFS needs a
third state — grey for *currently on the stack* — and an edge into a grey node
is a cycle.

Pattern C in [[../lessons/12-advanced-graphs|Lesson 12]].

## When to reach for it

Trigger: "can all tasks be completed", "is this dependency set satisfiable",
"return empty if the constraints contradict each other", "find the invalid
edge" — **on a directed graph**.

If the graph is undirected, don't use this: a two-state visited set plus "ignore
the node I came from" is enough, and union-find is often neater still.

If you're already running Kahn's algorithm for a topological order, **don't use
this either** — Kahn's detects the cycle for free (if fewer nodes come out than
went in, the remainder is a cycle). Colours are for when you're doing the DFS
anyway.

## How it works

| Colour | Meaning |
|---|---|
| white | not visited |
| grey | entered, not yet finished — **on the current DFS stack** |
| black | fully explored, all descendants done |

```
     entering                leaving
white ────────► grey ────────────► black
                 │
                 └── an edge INTO a grey node = back edge = CYCLE
```

The rule: **grey = cycle, black = fine.** Grey means the node is an *ancestor*
of where I am standing right now, so reaching it again means I walked in a
circle. Black means I already explored everything beyond it and found no cycle,
so there is nothing left to check.

### Why two states isn't enough

```
        A
       ╱ ╲
      ▼   ▼
     B     C
      ╲   ╱
       ▼ ▼
        D
```

DFS goes A→B→D (all marked visited), returns, then A→C→D. D is already visited.
A two-state scheme calls that a cycle. **There is no cycle** — D just has two
parents. This is a *cross edge*, and it is harmless. With colours, D is **black**
on the second visit, not grey, so no false positive.

## Minimal example

```python
WHITE, GREY, BLACK = 0, 1, 2

def has_cycle(adj, nodes):
    colour = {n: WHITE for n in nodes}

    def dfs(u):
        colour[u] = GREY
        for v in adj.get(u, ()):
            if colour[v] == GREY:      # back edge into an ancestor
                return True
            if colour[v] == WHITE and dfs(v):
                return True
        colour[u] = BLACK              # only AFTER the loop
        return False

    return any(colour[n] == WHITE and dfs(n) for n in nodes)
```

The placement of `colour[u] = BLACK` carries the whole idea: it happens on the
way *out* of the recursion, not on the way in. A node is grey for exactly as
long as it sits on the stack.

## Complexity

**O(V + E)** time — each node coloured once, each edge examined once. **O(V)**
space for the colour map, plus **O(V)** for the recursion stack.

That recursion stack is a real constraint in Python: the default limit is ~1000
frames, so a path-shaped graph of 10⁵ nodes overflows. Iterative DFS or Kahn's
avoids it.

## Gotchas

- **Two states on a directed graph.** The diamond above. The single most common
  version of this mistake.
- **Marking black too early.** Setting it before recursing into the children
  collapses the scheme back to two states, silently.
- **Only starting from one node.** A disconnected graph needs a loop over every
  white node; a cycle can live in a component the first DFS never reaches.
- **Applying it to an undirected graph.** Every undirected edge looks like a
  back edge to its own parent. Use the parent check there instead.
- **Python recursion limit.** See above.

## Related

- [[topological-sort]] — same computation, different output: an order exists
  exactly when no cycle does. Owned by [[../lessons/11-graphs|Lesson 11]]
- [[implicit-graph]] — the modelling step that usually produces the graph this
  check runs on
- [[seen-set]] — the two-state version, which is correct for undirected graphs
  and for plain reachability, and which this is the directed-graph upgrade of

## Evidence

Problems where I used this unaided:
