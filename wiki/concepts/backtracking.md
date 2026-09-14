---
type: concept
status: unseen
updated: 2026-09-14
sources: [https://www.geeksforgeeks.org/introduction-to-backtracking-data-structure-and-algorithm-tutorials/]
tags: [backtracking, recursion]
---

# Backtracking

**In one sentence, in my own words** — a depth-first walk over a tree of
decisions, where each frame *makes* a choice on the way down and *unmakes* it on
the way out, so the shared state is always exactly what the next branch expects.

Taught in [[../lessons/09-backtracking|Lesson 9]].

## When to reach for it

Trigger: the problem asks for **all** of something — all combinations, all
subsets, all paths, all arrangements — or asks whether *any* arrangement exists
and there's no formula for it. Words like "return every", "list all ways",
"find a path such that".

The negative trigger matters too: if the problem asks for a **count** or the
**best** one, backtracking is still an answer but often the wrong one — that's
usually where DP belongs, and the tell is overlapping subproblems.

## How it works

```python
def backtrack(state):
    if is_complete(state):
        res.append(path[:])          # snapshot, not the live list
        return
    for choice in candidates(state):
        if not is_valid(choice):     # prune before you recurse
            continue
        path.append(choice)          # 1. choose
        backtrack(advance(state))    # 2. explore
        path.pop()                   # 3. un-choose
```

Everything specific to a problem lives in three slots: `candidates()`,
`is_valid()`, and what `state` carries. The skeleton never changes.

The two branching styles:

| Style | Shape | Use for |
|---|---|---|
| Pick-or-skip | two calls per index, no loop | subsets, subsequences |
| Loop from `start` | one call inside a loop over `range(start, n)` | combinations, target sums |

In the loop form the index you pass down is the whole design decision:
`j + 1` = use each element once; `j` = reuse allowed; `0` = order matters
(permutations, and usually a bug if you didn't mean it). Starting the loop at
`start` is what prevents duplicate combinations *structurally*, rather than
generating `[2,3]` and `[3,2]` and filtering one out afterwards.

## Minimal example

```python
def subsets(nums):
    res, path = [], []

    def backtrack(start):
        res.append(path[:])              # every node is an answer here
        for j in range(start, len(nums)):
            path.append(nums[j])
            backtrack(j + 1)             # j+1 → each element used once
            path.pop()

    backtrack(0)
    return res
```

## Complexity

Time **O(k · bᵈ)** — `b` branches, `d` depth, so bᵈ nodes, times O(k) to copy a
completed path. Subsets: O(n·2ⁿ). Permutations: O(n·n!).

Space in **two parts, and say both**: O(d) auxiliary — only one root-to-node
path is live at a time, held by the call stack and the single `path` list — plus
O(total output), which is exponential because the answer is.

Pruning does not improve the worst-case bound. It is often the difference
between accepted and TLE anyway, and that's a legitimate thing to say.

## Gotchas

- `res.append(path)` stores a reference to a list that keeps mutating; the
  result ends up full of identical or empty lists. Always `path[:]`.
- A missing `pop` makes answers grow monotonically through the run.
- `start = 0` where `j` or `j + 1` belonged: too many answers, with duplicates.
- Deduping with a set at the end instead of preventing duplicates with the index.
- Claiming polynomial complexity. If you're enumerating, it's exponential — name
  the branching factor and depth and note the output is exponential too.

## Related

- [[decision-tree]] — the tree this walks; that page is the *picture*, this one
  is the *procedure*
- [[state-restoration]] — the un-choose half, generalised past lists to grids
  and boards
- [[seen-set]] — a *permanent* visited marker; backtracking's marks are
  temporary, which is exactly the difference between flood fill and path search

## Evidence

Problems where I used this unaided:
