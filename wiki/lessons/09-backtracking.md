---
type: lesson
topic: backtracking
status: ready
updated: 2026-09-14
sources: [https://www.geeksforgeeks.org/introduction-to-backtracking-data-structure-and-algorithm-tutorials/, https://www.geeksforgeeks.org/backtracking-algorithms/, https://docs.python.org/3/library/itertools.html]
tags: [lesson, backtracking]
---

# Lesson 9 — Backtracking

**Curriculum:** [[../curriculum/backtracking|Topic 9]] · **Materials:** [[../meta/resources|Resources]]
**Prerequisites:** [[../curriculum/stack|Topic 4 — Stack]] (the call stack *is* a stack)
and [[../curriculum/trees|Topic 7 — Trees]] (DFS, and recursion that carries state down).
**Work through in:** 60–90 minutes. Only 2 problems in this topic, but the
template you build here comes back in Tries, Graphs, and both DP topics.

---

## 1. Why this topic

The obvious reason: some problems ask for **every** answer, not the best one or
the count — every combination, every path, every arrangement. There's no clever
O(n) trick. You have to enumerate, and backtracking is how you enumerate without
writing n nested for-loops.

The real reason is a shift in how you use recursion.

Up to now, recursion has been a **function that returns a value**. In
[[07-trees|Trees]], `depth(node)` returned a number; the frame did its work,
handed the answer up, and vanished. Nothing survived it.

Backtracking is recursion where **the frames share one mutable object** — a
`path` list that every frame appends to on the way down and pops from on the way
out. The return value is often `None`. The answer accumulates in a variable the
recursion doesn't own.

That's the mental jump, and it's why people who can happily write a tree
traversal still freeze on Combination Sum. Once you see the recursion as *a
cursor walking a tree of decisions, leaving the world exactly as it found it*,
both problems in this topic are the same problem.

It also forces you to get honest about complexity. This is the first topic where
the right answer is exponential and you have to **say so out loud, precisely**,
instead of hoping the interviewer doesn't ask.

---

## 2. The mental model

### Exploring a maze with a piece of chalk

You're in a maze with no map. At each junction you pick a corridor, **chalk an X
on the floor behind you** so you don't loop, and walk on. If you hit a dead end
— or a wall, or you've already gone too far — you walk back to the junction,
**rub the X out**, and take the next corridor.

Two halves, and beginners only ever write the first:

1. **Chalk it** (choose) — commit to this branch.
2. **Rub it out** (un-choose) — leave the floor clean for the *next* branch.

If you never rub the X out, the second corridor you try still thinks you're
standing in the first one. That's the whole bug class in this topic.

### The decision tree

Every backtracking problem is a tree you never build. Nodes are *partial
answers*; edges are *decisions*; leaves are *complete answers or dead ends*.

Take "all subsets of `[1,2,3]`", branching on **take it or skip it**:

```
                        []
              take 1 ↙        ↘ skip 1
            [1]                  []
       ↙        ↘           ↙        ↘
    [1,2]        [1]      [2]         []
    ↙   ↘        ↙  ↘     ↙  ↘       ↙  ↘
[1,2,3] [1,2] [1,3] [1] [2,3] [2]  [3]  []
```

Eight leaves, 2³, because three decisions with two options each. The recursion
does a **depth-first walk of this tree** — leftmost path all the way down, then
back up one level and take the next edge. The `path` list is literally the
sequence of edges from the root to wherever you're standing. See
[[../concepts/decision-tree]].

Look at what happens between `[1,2,3]` and `[1,3]`: the walk has to *undo* two
decisions to get back to `[1]` before it can take the "skip 2" edge. Those undos
are the pops. **Backtracking is DFS plus an undo on the way out.**

---

## 3. The mechanics

### The template — write this from memory

Every backtracking solution you will ever write is this shape:

```python
def solve(...):
    res = []
    path = []

    def backtrack(state):
        if is_complete(state):
            res.append(path[:])          # COPY — see below
            return
        for choice in candidates(state):
            if not is_valid(choice):     # pruning
                continue
            path.append(choice)          # 1. choose
            backtrack(advance(state))    # 2. explore
            path.pop()                   # 3. un-choose

    backtrack(initial)
    return res
```

Choose / explore / un-choose. Everything in this topic is a question about what
goes in `candidates()`, what goes in `is_valid()`, and what `state` needs to
carry. The skeleton never changes.

### Why the un-choose is mandatory

`path` is **one list, shared by every frame.** There is no copy per frame. When
frame 3 appends, frames 1 and 2 see it too — they're all looking at the same
object. So a frame that appended and then returned without popping has left
garbage in a structure its parent is about to reuse.

```
path = [1]      →  append 2  →  path = [1,2]  →  recurse
                                              ←  return (no pop!)
                   append 3  →  path = [1,2,3]     ← WRONG, wanted [1,3]
```

The invariant to state out loud in an interview: **every call leaves `path`
exactly as it found it.** Append and pop are a matched pair, like a bracket.
See [[../concepts/state-restoration]].

### The copy-on-append trap

```python
res.append(path)      # WRONG — stores a REFERENCE to the live list
res.append(path[:])   # RIGHT — stores a snapshot
```

`path` keeps mutating after you've stored it. If you append the reference, every
entry in `res` points at the same list, and when the recursion finishes and
`path` has been popped back to empty, **`res` is a list of empty lists.** The
symptom is bizarre — the right *number* of answers, all of them wrong — which is
why it eats ten minutes when you hit it at minute 22.

`path[:]`, `list(path)`, and `path.copy()` are all the same thing. Pick one and
always use it.

### Where the memory goes

You are not building the tree. At any instant only **one root-to-node path**
exists: the chain of live stack frames, plus the one `path` list. That's O(depth)
of working memory, not O(2ⁿ).

The output is separate, and it's usually the dominant cost: 2ⁿ subsets of length
up to n is O(n·2ⁿ) space just to hold the answer. Say both numbers in an
interview — "O(depth) auxiliary, plus O(output) for the result" — because
interviewers do probe whether you've confused the two.

### The alternative: pass copies instead of undoing

```python
backtrack(i + 1, path + [choice])     # no append, no pop
```

This works, and it's shorter. It also allocates a fresh list at **every node of
the tree**, which adds an O(depth) factor to the runtime. Know that it exists,
mention it as the trade, and then write the append/pop version — it's the one
interviewers expect and the one that generalises to grids, where "pass a copy of
the board" is absurd.

---

## 4. Complexity

There is no hiding it and no reason to try. State the shape, then the number.

| Problem shape | Time | Why |
|---|---|---|
| Subsets — take/skip each of n | O(n · 2ⁿ) | 2ⁿ leaves, O(n) to copy each path |
| Permutations of n distinct | O(n · n!) | n! orderings, O(n) to copy each |
| Combinations, choose k from n | O(k · C(n,k)) | one leaf per combination |
| Unbounded sums to target `t`, min candidate `m` | O(bᵗᐟᵐ) | tree of depth t/m, branching b |
| Grid path of length L, 4 neighbours | O(cells · 3^L) | 3 not 4 — never step back where you came from |
| Auxiliary space, all of the above | O(depth) | one live path + the call stack |

The honest interview sentence is a formula, not a hand-wave:

> "The recursion tree has branching factor **b** and depth **d**, so O(bᵈ) nodes,
> and I do O(k) work at each leaf to copy the path — O(k·bᵈ). Auxiliary space is
> O(d) for the stack. Pruning cuts the constant hard in practice but not the
> worst-case bound."

Then, unprompted: **"the output itself is exponential, so no algorithm can beat
that bound."** That single sentence turns "your solution is exponential" from a
weakness into a fact about the problem, and it's the difference between a
candidate who's guessing and one who understands the question.

---

## 5. The patterns

Each is **a trigger in the problem statement → the tool.** The trigger is the
transferable half.

### Pattern A — pick-or-skip → [[../concepts/decision-tree]]
> **Trigger:** "all subsets", "all subsequences", "every way to include or exclude", each element is independently in or out

Binary branching at each index. Two recursive calls, no loop:

```python
def backtrack(i):
    if i == len(nums):
        res.append(path[:]); return
    path.append(nums[i]); backtrack(i + 1); path.pop()   # take
    backtrack(i + 1)                                      # skip
```

Depth n, branching 2, so 2ⁿ leaves. Use this when the choice at each position is
genuinely binary and the **order of the output within a subset doesn't matter**.

### Pattern B — loop over candidates from index `i` → [[../concepts/backtracking]]
> **Trigger:** "all combinations", "any number of times", "sums to a target", "choose k from n"

One recursive call inside a loop, and the loop **starts at a passed-in index**:

```python
def backtrack(start, remaining):
    for j in range(start, len(candidates)):
        path.append(candidates[j])
        backtrack(???, remaining - candidates[j])   # ← the whole question
        path.pop()
```

The `???` is the single most important decision in this topic:

| You pass | Meaning | Effect |
|---|---|---|
| `j + 1` | each element usable **once** | combinations |
| `j` | each element **reusable** | combinations with repetition |
| `0` | any element, any order | permutations-with-repeats — usually a bug |

**Why `start` matters: it enforces non-decreasing order, and that is what kills
duplicates.** `[2,3]` and `[3,2]` are the same combination. By never looking
backwards past `start`, the recursion can only ever produce the sorted version,
so the duplicate is never generated — not generated-then-filtered. Filtering
with a `set` of tuples afterwards also "works" and is a much weaker answer;
interviewers are testing whether you can prevent the duplicate structurally.

If the candidate list itself contains duplicate *values*, that's a different
problem again: sort first, and skip `j` where `j > start and c[j] == c[j-1]`.
Not needed for this topic's two problems, but it's the standard follow-up.

### Pattern C — pruning → the only thing between you and a timeout
> **Trigger:** any target/budget/limit in the statement — "sums to target", "at most k", "no two adjacent"

A dead branch you *enter* costs you the whole subtree beneath it. A dead branch
you *never enter* costs nothing. So test feasibility **before** recursing:

```python
if remaining - candidates[j] < 0:
    continue                      # or: break, if the candidates are sorted
```

Sorting the candidates first upgrades `continue` to `break` — once one option
overshoots, every later one does too, so you abandon the entire rest of the loop.
That's a real algorithmic difference, not a micro-optimisation, and it's a very
common "can you make it faster?" follow-up.

Three pruning cuts worth knowing by name:
- **Infeasible** — this choice already violates a constraint. Skip it.
- **Bound** — even the best possible completion can't reach the target. Abandon.
- **Symmetry** — this branch produces a permutation of one already emitted.
  That's exactly what `start` in Pattern B is doing.

### Pattern D — grid backtracking, mark and unmark → [[../concepts/state-restoration]]
> **Trigger:** "path through a grid", "adjacent cells", "each cell used at most once in a path", "word in a board"

Same template, but the state being chosen isn't a list entry — it's **the grid
itself.** You mark a cell as occupied, recurse into its four neighbours, then
restore it:

```python
def dfs(r, c, ...):
    if not (0 <= r < rows and 0 <= c < cols): return False
    if board[r][c] in USED_OR_WRONG: return False

    tmp = board[r][c]
    board[r][c] = '#'                  # 1. choose — mark visited
    found = (dfs(r+1, c, ...) or dfs(r-1, c, ...)
             or dfs(r, c+1, ...) or dfs(r, c-1, ...))
    board[r][c] = tmp                  # 3. un-choose — restore

    return found
```

The difference from the [[07-trees|tree DFS]] you already know: a tree has no
cycles, so DFS never needs a visited marker. A **grid does** — you can walk in
circles, or straight back into the cell you came from. And the difference from
a plain [[../concepts/seen-set|seen-set]] flood fill is that the mark is
**temporary**: a cell used in one candidate path must be free again for the
next. Global visited set → wrong answers. Restore on the way out → correct.

Two implementation choices, both fine, say which you picked and why:
- **Mutate the board** with a sentinel like `'#'`. O(1) extra space; mutates the
  caller's input, which you should acknowledge (and restore fully before
  returning).
- **Carry a `visited` set of `(r, c)`** with `add` / `discard`. O(L) space,
  doesn't touch the input. Safer if the interviewer cares about purity.

### Pattern E — the used-set for permutations
> **Trigger:** "all orderings", "arrangements", order matters

Permutations can't use `start` — every remaining element is a legal next choice
regardless of index. So you loop over *all* candidates and exclude the ones
already on the path with a `used` boolean array or set. Not required by this
topic's two problems, but it's the third shape and it shows up constantly in
follow-ups, so know that it's the answer to "what if order matters?"

---

## 6. Python notes

```python
import sys
sys.setrecursionlimit(10000)    # default is ~1000; rarely needed for LeetCode

res, path = [], []

res.append(path[:])             # snapshot. NOT res.append(path)
path.append(x); ...; path.pop() # matched pair. Always.

DIRS = ((1,0), (-1,0), (0,1), (0,-1))
for dr, dc in DIRS:             # cleaner than four hand-written calls
    dfs(r + dr, c + dc)
```

Closures beat parameter-passing for the things that never change:

```python
def exist(board, word):
    rows, cols = len(board), len(board[0])

    def dfs(r, c, i):           # only the varying state is a parameter
        ...                      # board, word, rows, cols come from the closure
    ...
```

Fewer parameters means fewer chances to pass the wrong one at minute 25.

**`itertools` exists and you should mention it, then not use it.**
[`itertools.combinations`, `permutations`, `product`](https://docs.python.org/3/library/itertools.html)
generate exactly these trees in C. In an interview, saying "in production I'd
reach for `itertools.combinations` — but this problem is asking me to write the
search, so:" is a strength. Leading with it instead of writing the recursion is
not.

**Mutable default arguments** are the classic Python landmine here:

```python
def backtrack(path=[]):   # the SAME list persists across calls. Don't.
```

Build `path` in the enclosing scope instead.

---

## 7. Traps

- **`res.append(path)` instead of `path[:]`.** The number of answers is right,
  the contents are all empty or all identical. Ten minutes gone. Check this
  *first* when the output is nonsense.
- **Forgetting the pop.** Answers get longer and longer as the run proceeds —
  that's the signature. Write `append` and `pop` in the same keystroke burst,
  before writing the recursive call between them.
- **Restoring the grid on the success path but not the failure path** (or
  returning early from inside the four-neighbour block, past the restore). Every
  exit from the frame must pass the restore.
- **Using a permanent `visited` set in a grid path problem.** Correct for flood
  fill / island counting, wrong here: it stops a later path from reusing a cell
  it's legitimately allowed to use.
- **`start = 0` when you meant `j` or `j + 1`.** Silently produces permutations
  instead of combinations, so you get far too many answers and duplicates. Say
  the reuse rule out loud before you write the call.
- **Filtering duplicates at the end with a set.** Works; reads as not
  understanding the structure. Prevent them with the index instead.
- **No pruning.** The difference between passing and TLE on Combination Sum.
- **Claiming a polynomial complexity.** If you're enumerating, it's exponential.
  Own it, name the branching factor and the depth, and note that the output is
  exponential too.
- **Not stating auxiliary vs output space.** O(depth) of stack; O(total answers)
  of result. Two different numbers.
- **Not checking the base case fires.** Off-by-one in `if i == len(nums)` versus
  `i > len(nums)` gives infinite recursion or a missing level.

---

## 8. Worked example

Outside the problem set, so nothing is spoiled.

> **Problem.** Given a string of letters and digits, return every string you can
> make by changing each letter to lowercase or uppercase, in any order.
> `"a1b"` → `["a1b", "a1B", "A1b", "A1B"]`. Digits never change.

**Restate it as decisions.** Walk the string left to right. At each position:
if it's a digit, there is **one** choice (keep it); if it's a letter, there are
**two** (lower or upper). Complete answer when the position runs off the end.
That's a decision tree with branching 1 or 2 and depth n — Pattern A.

```
                   ""
          a ↙            ↘ A
        "a"              "A"
         │1               │1          ← digits don't branch
       "a1"             "A1"
      ↙    ↘           ↙    ↘
  "a1b"  "a1B"     "A1b"  "A1B"
```

**Brute force first**, as always: generate all 2ⁿ case-combinations of the whole
string and discard the ones that mangle digits. Same exponential, more waste —
and the waste is the tell that the *branching itself* should be constrained.

```python
def letter_case_permutations(s):
    res, path = [], []

    def backtrack(i):
        if i == len(s):
            res.append("".join(path))      # join makes a new string — snapshot for free
            return
        ch = s[i]
        if ch.isdigit():
            path.append(ch)                # choose (the only option)
            backtrack(i + 1)
            path.pop()                     # un-choose
        else:
            for variant in (ch.lower(), ch.upper()):
                path.append(variant)       # 1. choose
                backtrack(i + 1)           # 2. explore
                path.pop()                 # 3. un-choose

    backtrack(0)
    return res
```

**Complexity.** Let `k` be the number of letters. 2ᵏ leaves, O(n) to join each:
**O(n · 2ᵏ)** time. Auxiliary space O(n) — the `path` list and the call stack,
one live root-to-leaf chain at a time. Output space O(n · 2ᵏ), which is a lower
bound for any solution because that's just how big the answer is.

**Three things to carry out of this:**

1. `"".join(path)` sidesteps the copy trap by accident — it builds a new string
   every time. With a list result, `path[:]` is doing the same job explicitly.
   Know *why* you're safe, don't just be safe.
2. The digit branch still does choose/explore/un-choose even though there's only
   one choice. Keeping the template uniform is what stops you forgetting a pop
   in the branch you wrote in a hurry.
3. Constraining the branching (1 option for digits, not 2-then-filter) is
   pruning in its cheapest form — Pattern C. Never generate what you'd only
   throw away.

---

## 9. Materials

Full list on [[../meta/resources|Resources]]. In order:

1. [Introduction to backtracking](https://www.geeksforgeeks.org/introduction-to-backtracking-data-structure-and-algorithm-tutorials/)
   — 10 min. The choose/explore/un-choose frame, with the recursion tree drawn out.
2. [Backtracking algorithms hub](https://www.geeksforgeeks.org/backtracking-algorithms/)
   — skim the problem list; notice how many are the same template with a
   different `is_valid()`.
3. [`itertools` docs](https://docs.python.org/3/library/itertools.html) — read
   `combinations`, `permutations`, `product`. Know what you're reimplementing.
4. **N-Queens** — search YouTube for a visualisation of the board backtracking.
   Not in the Blind 75, so it's safe to watch, and it's the clearest picture of
   pruning doing real work.
5. [NeetCode on YouTube](https://www.youtube.com/@NeetCode) — search the problem
   name, but **only after** a timed attempt and a debrief.

---

## 10. Self-check

Answer these **aloud**, in full sentences, before starting the timer.

1. Write the choose / explore / un-choose template from memory, without looking.
2. Why does `path` have to be un-chosen? What exactly breaks if you skip the pop?
3. What is stored in `res` if you write `res.append(path)`, and what does the
   final output look like?
4. In a loop-over-candidates backtrack, what's the difference between recursing
   with `j`, `j + 1`, and `0`?
5. How does a start index prevent duplicate combinations — and why is that
   better than deduping at the end?
6. Why is grid backtracking O(cells · 3ᴸ) and not O(cells · 4ᴸ)?
7. Why can't you use a permanent visited set for a grid *path* problem, when you
   can for island counting?
8. State the space complexity of a subsets solution in two parts, and say which
   part is auxiliary.
9. An interviewer says "that's exponential." What's your reply?
10. Give the trigger phrase for each of patterns A–E.

---

## 11. Ready?

**First timed problem: Combination Sum** (Medium) — Pattern B, and it will
punish you if you skip Pattern C.

Say `timed combination-sum` and I'll give you the statement and start the
30-minute box. No hints while it's running.

**What "good" looks like in 30 minutes:**

- You draw the decision tree — on paper, for a tiny input — **before** writing
  code. Two minutes spent here saves fifteen later. This is the topic where
  drawing first is non-negotiable.
- You say out loud, before you write the recursive call, whether a candidate can
  be reused, and therefore whether you're passing `j` or `j + 1`.
- The template comes out as a unit: append, recurse, pop, in one motion.
- You write `path[:]`, and you can say why without being asked.
- You identify at least one prune and implement it.
- You state the complexity as branching-and-depth, not as a guessed exponent,
  and you volunteer that the output is exponential too.
- You trace one small input by hand before claiming it works.

This is a Medium and it's the first problem in a genuinely new mode of thinking.
**Not finishing is expected.** If you get a correct-but-slow enumeration and run
out of time before pruning it, that is a good box, not a bad one — the enumeration
is the hard half.

Either way it goes in the [[../meta/review-queue|review queue]] and comes back
around. **The plan is to repeat until it's cold-solvable, not to clear the list
once.**
