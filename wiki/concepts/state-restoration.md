---
type: concept
status: unseen
updated: 2026-09-14
sources: []
tags: [backtracking, recursion]
---

# State Restoration

**In one sentence, in my own words** — when recursive frames share one mutable
object, every frame must leave it exactly as it found it, so a modification and
its undo are a matched pair wrapped around the recursive call.

The "un-choose" half of [[backtracking]]. Taught in
[[../lessons/09-backtracking|Lesson 9]].

## When to reach for it

Trigger: **any time recursion mutates something it didn't create.** A `path`
list defined in the enclosing scope, a grid cell, a `visited` set, a running
count of used columns. If the object outlives the frame, the frame owes it a
restore.

The tell that you needed this and didn't do it: answers that **grow as the run
proceeds** (missing `pop`), or a grid where later paths can't use cells that
earlier paths touched (missing unmark).

The opposite trigger — when you *don't* need it: if the state is permanent by
design, like a visited set in island counting or cycle detection, restoring it
would be the bug. Ask "may a later branch legitimately reuse this?" Yes →
restore. No → don't.

## How it works

```python
path.append(choice)     # modify
backtrack(...)          # recurse
path.pop()              # restore — matched pair, like a bracket
```

For a grid, the same shape with a sentinel value:

```python
tmp = board[r][c]
board[r][c] = '#'                   # mark visited
found = any(dfs(r+dr, c+dc) for dr, dc in DIRS)
board[r][c] = tmp                   # unmark — on EVERY exit path
return found
```

The invariant, worth stating aloud in an interview: **the state on entry to a
frame equals the state on exit from it.** That one sentence is what lets a
reader believe the sibling branches are independent.

The alternative is to make copies instead — `backtrack(path + [choice])` — which
removes the undo entirely at the cost of an allocation per tree node. Fine for
small lists, absurd for a grid. Know the trade, then write the mutate/restore
version.

## Minimal example

The bug, made visible:

```python
path = [1]
path.append(2)          # [1, 2]
recurse()               # ... returns without popping
path.append(3)          # [1, 2, 3]   ← wanted [1, 3]
```

Frames 1 and 2 are not looking at copies. There is one list.

## Complexity

Free — O(1) per modification, and it happens once per tree edge, so it doesn't
change the O(bᵈ) bound at all.

What it *buys* is space: mutate-and-restore keeps auxiliary space at **O(depth)**
where copy-per-node would push work (and garbage) to O(depth) per node, an extra
factor across the whole tree.

## Gotchas

- **Early `return` that skips the restore.** The most expensive version of this
  bug, because it only fires on some inputs. Either have a single exit point, or
  restore before every `return`.
- Restoring on the success path but not the failure path in a grid DFS.
- Restoring the wrong thing — `board[r][c] = '.'` instead of the saved `tmp`.
  Save the original; don't assume what it was.
- Using a permanent `visited` set where a temporary mark belonged. Correct for
  flood fill, silently wrong for path search.
- Mutating the caller's input and not saying so. If the board must be pristine
  afterwards, prove that it is; otherwise carry a `visited` set instead.

## Related

- [[backtracking]] — the full choose/explore/un-choose template this is half of
- [[decision-tree]] — restoring is what "walking back up an edge" means in code
- [[seen-set]] — the permanent counterpart; the entire difference is whether a
  later branch is allowed to reuse what an earlier one touched

## Evidence

Problems where I used this unaided:
