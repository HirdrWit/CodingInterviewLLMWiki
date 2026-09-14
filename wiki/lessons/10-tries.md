---
type: lesson
topic: tries
status: ready
updated: 2026-09-14
sources: [https://www.geeksforgeeks.org/trie-insert-and-search/, https://www.geeksforgeeks.org/advantages-trie-data-structure/, https://www.geeksforgeeks.org/complexity-cheat-sheet-for-python-operations/]
tags: [lesson, tries]
---

# Lesson 10 — Tries

**Curriculum:** [[../curriculum/tries|Topic 10]] · **Skill:** [[../skills/data-structures|Data Structures]] · **Materials:** [[../meta/resources|Resources]]
**Prerequisites:** [[../curriculum/arrays-hashing|Topic 1]] (hash maps), [[../curriculum/trees|Topic 7]] (recursion over a tree), [[../curriculum/backtracking|Topic 9]] (grid backtracking — needed for problem 3).
**Work through in:** 45–60 minutes. Only three problems, but the third is a Hard
that fuses this topic with the last one, so the mental model has to be clean
before you start.

---

## 1. Why this topic

The obvious reason: three Blind 75 problems use a trie, and they're not solvable
without one.

The real reason is a gap in what Topic 1 taught you. A hash set answers **"is
this exact word present?"** in O(1). That felt like it answered everything. It
doesn't. Ask it instead:

> *Is there any word in this dictionary that starts with `"pre"`?*

The hash set has no answer. Hashing deliberately **destroys** the relationship
between a key and its neighbours — `"pre"`, `"press"` and `"pretend"` land in
three unrelated buckets by design, because a good hash function scatters. So the
only way to answer a prefix question with a set is to scan every key: O(n × L).

A trie fixes this by making **the prefix itself the addressable thing.** A word
is not stored in one place; it's a *path*, and every node along that path *is* a
prefix. Asking about a prefix becomes walking to a node.

That reframe — *store the structure of the keys instead of scattering them* — is
the transferable idea. It's also the first time in the Blind 75 you're asked to
**design a data structure** rather than use one, which is a different interview
skill: [[../skills/code-quality|clean class structure]] gets graded here in a way
it doesn't when you're writing a single function.

---

## 2. The mental model

### A trie is a filing cabinet with one drawer per letter

Not a dictionary of words. A **tree of letters**, where the letter lives on the
*edge* you take, and the node is "everywhere you've got to so far".

Start at the root, which is the empty prefix `""`. To store `"cat"`, you walk:
take the `c` edge, then the `a` edge, then the `t` edge, creating drawers as you
go. To store `"car"`, you walk the same `c` and `a` edges — they already exist —
and only then branch off with a new `r`.

Here is a trie holding `cat`, `car`, `card`, `dog`:

```
                (root)
                /    \
              c       d
             /         \
            c*          d*          * = a node; the letter above it is the edge
           /             \
          a               o
         /                 \
        a*                  o*
       /  \                  \
      t    r                  g
     /      \                  \
   t[■]    r[■]                g[■]
             \
              d
               \
              d[■]

   [■] = is_end — "a complete word ends here"
```

Flattened, the way it's actually drawn:

```
root
 ├── c
 │    └── a
 │         ├── t ■        ("cat")
 │         └── r ■        ("car")
 │              └── d ■   ("card")
 └── d
      └── o
           └── g ■        ("dog")
```

Read three things off that picture:

1. **`car` and `card` share their whole first three levels.** Common prefixes
   are stored once. That's the compression a trie buys you.
2. **Depth is spelling.** The node at depth 3 down the `c-a-r` path *is* the
   prefix `"car"`. Nothing needs computing to find it — you walk.
3. **`r` is marked `■` even though the path continues.** `"car"` is a word and
   also a prefix of `"card"`. Without that flag you cannot tell the difference.

### Why the `is_end` flag is not optional

This is the part people get wrong, so hold it carefully.

"Has children" does **not** mean "is not a word" (`car`), and "has no children"
does **not** reliably mean "is a word" in any scheme you'd want to maintain. The
tree structure encodes *paths*; it does not encode *which paths are words*.

Insert `"card"` alone and then ask "is `car` in here?" — the walk succeeds, every
letter is present, and the honest answer is **no**. The only thing separating
"I reached this node" from "a word ends at this node" is the boolean you put
there. It is the single most common bug in a first trie implementation.

---

## 3. The mechanics — enough to reason about cost

### The node

One node is two fields, and that's the whole data structure:

```python
class TrieNode:
    def __init__(self):
        self.children: dict[str, "TrieNode"] = {}
        self.is_end: bool = False
```

See [[../concepts/prefix-tree-node|prefix-tree-node]]. Two design notes that
interviewers do probe:

- **`dict` vs a fixed array of 26.** A `dict[str, TrieNode]` costs a hash per
  step but only allocates the children that exist. A `[None] * 26` array indexed
  by `ord(c) - ord('a')` is faster per step and cache-friendlier, but burns 26
  slots per node whether or not they're used, and hard-codes the alphabet. For
  interviews: **use the dict** — it's shorter, it survives a "what about digits
  or unicode?" follow-up, and the constant factor is not what's being graded. Be
  ready to say why the array version exists.
- **The node stores no letter of its own.** The letter is the *key in the
  parent's dict* — the label on the edge, not on the node. Storing the character
  inside the node too is redundant state you then have to keep in sync.

### The three operations are one walk

Every trie operation is the same loop with a different ending:

```python
node = self.root
for ch in word:
    if ch not in node.children:
        return False          # insert: create it instead
    node = node.children[ch]
```

- **insert** — walk, creating missing children; set `is_end = True` at the last node.
- **search** — walk; fail if a letter is missing; at the end return `node.is_end`.
- **startsWith** — the identical walk; at the end return `True`. **The only
  difference between `search` and `startsWith` is whether you consult the flag.**

Notice what is *not* in that loop: the number of words already stored. You take
one step per character of the query, full stop. A trie holding ten words and a
trie holding ten million words answer `startsWith("pre")` in exactly three steps.

### The real space cost

The honest version, because "tries save space by sharing prefixes" is only half
true.

Worst case the trie holds one node per character of every inserted word:
**O(total characters)** nodes. Sharing only helps to the extent your words
actually share prefixes — a dictionary of random strings shares almost nothing.

And each node is a **Python object holding a dict**. That is not one byte per
character; it is on the order of a hundred-plus bytes per node once you count
the object header and the dict's own table. So a trie is usually *heavier* in
raw memory than the same words in a set, even though it stores fewer characters.

What you buy for that is the prefix query, plus the ability to **prune** — which
is what makes problem 3 tractable. Say this trade out loud in an interview: it
is exactly the "spend memory, buy time" trade from
[[../lessons/01-arrays-hashing|Lesson 1]], one level more sophisticated.

---

## 4. Complexity

`L` = length of the word or prefix in question. `n` = number of words stored.
`T` = total characters across all stored words. `A` = alphabet size (26 here).

| Operation | Time | Space | Why |
|---|---|---|---|
| `insert(word)` | **O(L)** | O(L) new nodes worst case | One step per character; new nodes only where the path diverges |
| `search(word)` | **O(L)** | O(1) | Same walk, then read `is_end` |
| `startsWith(prefix)` | **O(L)** | O(1) | Same walk, no flag read — *this is the operation a hash set cannot do* |
| Build from n words | O(T) | O(T) nodes | Sum of the inserts |
| Wildcard search (`.` matches any) | O(A^d × L) worst | O(L) recursion stack | Each `.` forks into every child; `d` = number of dots |
| Collect all words under a prefix | O(size of that subtree) | — | DFS from the prefix node |

The row to internalise: **every core operation is independent of `n`.** Compare
against the alternatives for "does any stored word start with `p`?":

| Approach | Prefix query | Note |
|---|---|---|
| Hash set of words | O(n × L) | Must scan everything — hashing destroyed the ordering |
| Sorted list + binary search | O(L log n) | Works, and is genuinely fine for a one-off lookup |
| **Trie** | **O(L)** | Plus you get "all words under here" and pruning for free |

Being able to name the sorted-array alternative and say *why the trie still wins
when you need the subtree* is a strong interview answer.

---

## 5. The patterns

Each is **a trigger in the problem statement → the tool**. The trigger is the
half that transfers.

### Pattern A — build the trie itself → [[../concepts/trie|concept page]]
> **Trigger:** "implement", "design a data structure", "support insert / search / startsWith", "prefix"

The word *prefix* appearing in an API spec is about as loud as triggers get.
When the problem hands you the method signatures, the design **is** the answer:
a `TrieNode` class with `children` and `is_end`, and a `Trie` class holding a
root. Three methods, one shared walk. Resist the urge to be clever — the graded
qualities here are naming, the absence of duplicated walk logic, and whether
`search` and `startsWith` differ by exactly one line.

### Pattern B — wildcard descent (trie + DFS) → [[../concepts/trie|concept page]]
> **Trigger:** "`.` can represent any letter", "matching with wildcards", "search may be ambiguous"

The moment a query character can match more than one edge, the walk stops being
a loop and becomes a **recursion**. At a normal character you take one edge. At a
wildcard you must try *every* child and succeed if any branch does.

```
query "c.t" against {cat, cut, cot, car}

           root
            │ c
            ▼
          (c)                     ← consumed 'c', one edge
         ╱  │  ╲
        a   u   o                 ← '.' forks: try ALL children
       ╱    │    ╲
     (a)   (u)   (o)
     ╱ ╲    │     │
    t   r   t     t
   ■   ■   ■     ■
   ↑                              ← first branch that ends on is_end wins
```

The shape is: *recurse, carrying the index into the query and the current node.*
Base case — index past the end of the query — returns `node.is_end`. That is the
generic trie-DFS skeleton, and Pattern C is the same skeleton with a second
thing being walked at the same time.

Cost: each dot multiplies the branching by up to the alphabet size, so a query
of all dots is O(A^L). Say that when asked; don't claim O(L).

### Pattern C — the trie as a pruner for another search
> **Trigger:** "find **all** the words from this list that appear in the grid/text", a set of *many* targets searched at once

This is the payoff pattern, and it's the one worth real study because it
generalises far beyond grids.

The naive approach to "which of these 3000 words appear in the grid" is: for
each word, run a search. 3000 independent searches, each re-exploring the same
early cells, most of them doomed after two letters.

Invert it. Put **all the words in one trie**, then explore the grid *once*,
carrying a trie node alongside your position. At every step you move in the grid
and move in the trie together. The moment the character under you is not a child
of the current trie node, **no word in the entire dictionary can continue this
way** — abandon the branch immediately.

```
grid walk               trie node
  o  →  a  →  t  →  h     root → o → a → t → ✗   (no 'h' child)
                                              │
                          3000 words eliminated by one dict lookup
```

That single failed lookup prunes what would have been thousands of separate
searches. The trie isn't storing the answer — it's **a shared decision table
that tells a backtracking search when to stop**, which is precisely what
[[../curriculum/backtracking|Topic 9]] said backtracking is always short of.

Two refinements you should be able to mention, without needing them to pass:

- **Mark the node, don't return early.** When you land on a node with
  `is_end = True`, record that word and *keep going* — a longer word may continue
  down the same path (`car` → `card`).
- **Prune the trie as you consume it.** Once a word has been found, it never
  needs finding again; deleting its leaf (and any node left childless) shrinks
  the decision table for the rest of the search. This is the optimisation that
  turns a timing-out solution into a fast one, and it's a genuinely impressive
  thing to volunteer.

I am not going to show you the grid-plus-trie code — that's problem 3 and you
get it cold. What you should carry in is the *shape*: **one DFS, two cursors,
prune when the trie says the prefix is dead.**

---

## 6. Python notes

```python
class TrieNode:
    __slots__ = ("children", "is_end")      # optional; real memory saving per node
    def __init__(self):
        self.children = {}
        self.is_end = False

node.children.setdefault(ch, TrieNode())     # get-or-create in one line
node = node.children.setdefault(ch, TrieNode())   # ...and step into it

if ch not in node.children:                  # dict membership: O(1) average
    return False
```

A few things worth knowing:

- **`setdefault` vs `defaultdict`.** `setdefault(ch, TrieNode())` constructs a
  node on every call even when one already exists — harmless but wasteful. A
  `defaultdict(TrieNode)` is the idiomatic alternative and makes insert two
  lines, but it also means **any lookup silently creates a node**, which quietly
  breaks `search` and `startsWith`. If you use `defaultdict`, read with
  `.get(ch)` in the read paths. Simplest safe version is the explicit
  `if ch not in node.children: node.children[ch] = TrieNode()`.
- **Recursion depth.** Python's default limit is 1000 frames. Word lengths in
  these problems are small, so a recursive wildcard search is safe — but know
  the limit exists, and know an iterative walk is available for the non-wildcard
  operations.
- **Don't use a `dict` of `dict`s with a magic key.** The `{"*": True}`
  end-marker hack you'll see online works, but it conflates data with structure
  and it reads badly. The class version is barely longer and is what an
  interviewer wants to see: see [[../skills/code-quality|code quality]].
- Dict operations are O(1) *average* — the same caveat as
  [[../concepts/hash-map|hash map]]. A trie step is a hash, not free.

---

## 7. Traps

- **Forgetting `is_end`.** Insert `"apple"`, then `search("app")` returns `True`.
  The canonical trie bug. It will be the first thing the test cases check.
- **`search` implemented as `startsWith`.** Same walk, and if you copy-paste one
  into the other you will forget to change the return. Write the shared walk once
  as a private helper that returns the node-or-`None`, then build both public
  methods on it. This also *shows* the interviewer you saw the duplication.
- **Returning `True` from a wildcard branch that failed.** In the DFS, you must
  return `True` only if a child branch returned `True`, and fall through to
  `False` after the loop. Writing `return self._dfs(child, ...)` inside the loop
  returns on the **first** child, which abandons the other branches. Use
  `if ...: return True`, then `return False` after.
- **Returning early on the first word found in problem 3.** `car` is not the
  only word down that path. Record and continue.
- **Not un-marking the grid cell on the way out.** That's a Topic 9 bug
  resurfacing under a Topic 10 problem — the backtracking half still needs its
  undo step. Fusing two patterns is exactly when people drop one's discipline.
- **Claiming the trie is O(1).** It is O(L). Small, but not constant, and saying
  "O(1)" when you mean "independent of the number of words" is a sloppiness an
  interviewer will pick at. Say the precise thing: *O(L), independent of n.*
- **Claiming a trie saves memory.** It saves *characters stored*; it usually
  costs more *bytes* than a set. Know which claim you're making.
- **Building the trie inside the loop.** In problem 3, build it once before the
  grid walk begins, not per cell.

---

## 8. Worked example

Outside the problem set, so nothing is spoiled. (LeetCode 648, *Replace Words* —
not in the Blind 75.)

> **Problem.** You're given a dictionary of roots, e.g. `["cat", "bat", "rat"]`,
> and a sentence, e.g. `"the cattle was rattled by the battery"`. Replace every
> word in the sentence that has a root in the dictionary with its **shortest**
> such root. → `"the cat was rat by the bat"`.

**Brute force.** For each word in the sentence, for each root in the dictionary,
check `word.startswith(root)`. With `w` words, `d` roots and length `L`:
O(w × d × L). Say this first, always — it proves you read the problem and gives
you a baseline.

**Spot the pattern.** The inner loop is asking *"is any of my dictionary a
prefix of this word?"* — many targets, one prefix question, asked repeatedly.
That's **Pattern A**, and the fact that we want the *shortest* root means we
want the **first `is_end` we hit walking down**. A hash set can't do this without
trying every prefix length; a trie does it in one walk.

```python
class TrieNode:
    def __init__(self):
        self.children = {}
        self.is_end = False


def replace_words(roots: list[str], sentence: str) -> str:
    root_node = TrieNode()
    for word in roots:                       # build: O(total chars in roots)
        node = root_node
        for ch in word:
            if ch not in node.children:
                node.children[ch] = TrieNode()
            node = node.children[ch]
        node.is_end = True

    def shortest_root(word: str) -> str:
        node = root_node
        for i, ch in enumerate(word):        # O(L) — one walk, no dictionary scan
            if ch not in node.children:
                return word                  # path died: no root is a prefix
            node = node.children[ch]
            if node.is_end:
                return word[: i + 1]         # first ■ = shortest root. Stop.
        return word

    return " ".join(shortest_root(w) for w in sentence.split())
```

**Complexity.** O(T + S) time, where `T` is the total characters in the roots and
`S` the length of the sentence — each is touched a constant number of times.
O(T) space for the trie. The dictionary size `d` has **vanished from the query
cost**, which is the entire point.

**The move to internalise:** the brute force had an inner loop whose only job was
to test many prefixes. Lesson 1 taught you to collapse a searching inner loop
with a hash structure; that only works for *exact* matches. When the inner loop
is searching by **prefix**, the trie is the structure that collapses it. Same
instinct, different cabinet.

---

## 9. Materials

Full list on [[../meta/resources|Resources]]. In order:

1. [Trie — insert and search](https://www.geeksforgeeks.org/trie-insert-and-search/) — 15 min. The canonical walkthrough; note how `is_end` (`isEndOfWord` there) is handled.
2. [Advantages of the trie data structure](https://www.geeksforgeeks.org/advantages-trie-data-structure/) — 5 min. The "why not just a hash set" argument, stated properly.
3. [Trie — delete](https://www.geeksforgeeks.org/trie-delete/) — 10 min, optional but it's the pruning idea from Pattern C, and it forces you to understand the node invariants.
4. Search YouTube for *"NeetCode Implement Trie"* — but **only after** your timed attempt. Channel: [NeetCode](https://www.youtube.com/@NeetCode).
5. Re-skim [[../lessons/09-backtracking|Lesson 9]] before problem 3. You need the
   visit/unvisit discipline intact, not half-remembered.

---

## 10. Self-check

Aloud, full sentences, before the timer starts. Any that come out mumbled, back
to section 3.

1. What question can a trie answer that a hash set cannot, and *why* can't the hash set answer it?
2. Draw a trie holding `to`, `tea`, `ted`, `ten`, `A`, `in`, `inn`. How many nodes?
3. Why is `is_end` necessary? Give a concrete two-word example where the trie is wrong without it.
4. What are the two fields of a trie node, and why does the node not store its own letter?
5. `search` and `startsWith` differ by how much code? What is it?
6. What is the time complexity of `insert`, and what does it *not* depend on?
7. Is a trie more or less memory-efficient than a set of the same words? Careful — say what you're measuring.
8. In wildcard search, what happens at a `.`, and what's the worst-case cost?
9. `dict` children vs a 26-slot array: name one advantage of each.
10. In Pattern C, what exactly does the trie prune, and how does one dict lookup eliminate thousands of searches?

---

## 11. Ready?

**First timed problem: Implement Trie (Prefix Tree)** (Medium) — Pattern A.

Say `timed implement-trie` and I'll give you the statement and start the
30-minute box. No hints while it's running.

**What "good" looks like here.** This is a *design* problem, so finishing isn't
the whole bar — the code is the deliverable and it gets read as code:

- Two classes: `TrieNode` (children + is_end) and `Trie` (holds a root). Not one
  class, not a nest of raw dicts.
- The walk written **once**. If `search` and `startsWith` both contain the same
  for-loop, refactor before you call it done — an interviewer watching you spot
  your own duplication is worth more than finishing two minutes sooner.
- `is_end` correct: `insert("apple")` then `search("app")` → `False`, but
  `startsWith("app")` → `True`. Test that specific pair unprompted.
- You state **O(L), independent of the number of words** without being asked, and
  you volunteer the space cost.
- Real names: `children`, `is_end`, `node`, `root`. Not `c`, `e`, `n`.

You should have time left. Roll straight into **Design Add and Search Words Data
Structure** — it reuses the class you just wrote and only changes `search`, so
it's the cheapest second rep available anywhere in the Blind 75. Leave **Word
Search II** for a separate box on a separate day, with Topic 9 fresh; it's a
Hard, and expecting to finish a first attempt on it is not realistic.

Not finishing is fine and expected. It goes in the
[[../meta/review-queue|review queue]] and comes back around. **The plan is to
repeat until it's cold-solvable, not to clear the list once.**
