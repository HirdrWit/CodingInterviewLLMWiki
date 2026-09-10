---
type: lesson
topic: arrays-hashing
status: ready
updated: 2026-09-10
sources: [https://www.geeksforgeeks.org/introduction-to-hashing-2/, https://www.geeksforgeeks.org/load-factor-and-rehashing/, https://www.geeksforgeeks.org/complexity-cheat-sheet-for-python-operations/]
tags: [lesson, arrays-hashing]
---

# Lesson 1 — Arrays & Hashing

**Curriculum:** [[../curriculum/arrays-hashing|Topic 1]] · **Materials:** [[../meta/resources|Resources]]
**Work through in:** 60–90 minutes, plus the videos. Don't rush it — this is the
topic everything else stands on.

---

## 1. Why this topic first

Two reasons, and the second one is the real one.

The obvious reason: arrays and hash maps are the two data structures you will
use in more than half of all interview problems. Even tree and graph problems
lean on a hash set for "have I visited this node".

The real reason: **this topic teaches the single most important trade in all of
DSA — spending memory to buy time.** Almost every "optimal" solution in the
Blind 75 is some version of *store what you've already seen so you never have to
look at it twice*. Arrays & Hashing is where that idea is at its clearest, with
nothing else in the way. Get the instinct here and it pays out for the next 67
problems.

---

## 2. The mental model

### An array is a street of numbered houses

Fixed-width boxes, laid out end to end in memory, all the same size.

```
index:      0     1     2     3     4
          ┌─────┬─────┬─────┬─────┬─────┐
values:   │  7  │  2  │  9  │  4  │  1  │
          └─────┴─────┴─────┴─────┴─────┘
address:  1000  1008  1016  1024  1032      (8 bytes each)
```

Because every box is the same size and they're contiguous, the computer finds
box `i` with arithmetic, not searching: `address = start + i × width`. One
multiply, one add. That's why **indexing is O(1)** — and it's the *only* thing
an array gives you for free.

Everything else costs. To insert at the front, every other house has to shuffle
down one: **O(n)**. To find a value when you don't know its index, you check
each box in turn: **O(n)**.

### A hash map is a coat check

You hand over a coat (the **key**). The attendant runs it through a rule that
turns it into a ticket number (the **hash function**), and hangs the coat on
that numbered hook. When you come back with the same coat, the same rule
produces the same number, and they walk straight to the hook.

```
   key "banana"
        │
        ▼
   ┌──────────┐
   │   hash   │   deterministic: same key → same number, always
   └──────────┘
        │  h = 8291043...
        ▼
   h % 8 = 3          ← squash into the number of buckets we have
        │
        ▼
buckets: 0    1    2    3    4    5    6    7
        ┌──┬────┬────┬────┬────┬────┬────┬────┐
        │  │    │    │ ●  │    │    │    │    │
        └──┴────┴────┴─┬──┴────┴────┴────┴────┘
                       └─→ ("banana", 4)
```

The magic is that **you never searched.** You computed where the thing lives.
That's the whole idea, and it's why a hash map turns "is this in here?" from
O(n) into O(1).

**A hash set is the same machine with the coats thrown away** — it stores only
the keys. Use a set when you care *whether* you've seen something, a map when
you care *what you saw with it*.

---

## 3. The mechanics — enough to reason about cost

You need three facts. Interviewers do ask about these.

**1. Collisions are inevitable.** You're squashing infinitely many possible keys
into a finite number of buckets, so two keys will eventually land on the same
hook. Real implementations handle it by
[separate chaining](https://www.geeksforgeeks.org/separate-chaining-collision-handling-technique-in-hashing/)
(each bucket holds a small list) or
[open addressing](https://www.geeksforgeeks.org/open-addressing-collision-handling-technique-in-hashing/)
(probe for the next free hook). Python uses open addressing.

**2. That's why it's O(1) *average*, not O(1) guaranteed.** If every key
collided into one bucket, lookup would degrade to scanning a list: **O(n) worst
case**. In practice, with a decent hash function, buckets stay short and lookup
is a constant handful of steps.

**3. The table grows.** The **load factor** is `entries ÷ buckets`. When it
crosses a threshold (~0.66 in CPython), the table allocates a bigger array and
**rehashes** every key into it. That single insert costs O(n) — but it happens
rarely enough that the cost *amortizes* to O(1) per insert. This is the same
amortization trick that makes `list.append` O(1).

> Read [Load factor & rehashing](https://www.geeksforgeeks.org/load-factor-and-rehashing/).
> It's the page that turns "hash maps are O(1)" from something you memorised
> into something you can defend under questioning.

**Keys must be immutable.** The bucket is chosen from the key's contents — if
you mutate a key after inserting it, it hashes somewhere else and the entry
becomes unreachable. This is why Python lets you use a `str`, `int`, or `tuple`
as a key, but not a `list`. It's also why **`tuple(...)` is the standard trick
for making a list usable as a key** — you'll need that in problem 4.

---

## 4. Complexity — the table to know cold

| Operation | Array / list | Hash map / set |
|---|---|---|
| Access by index | **O(1)** | n/a |
| Search for a value | O(n) | **O(1)** avg, O(n) worst |
| Insert / delete at end | O(1) amortized | **O(1)** avg |
| Insert / delete at front or middle | O(n) | **O(1)** avg |
| Ordered iteration | O(n), already in order | O(n log n) — must sort |
| Memory overhead | low, compact | ~2–3× — you're paying for the speed |

Read the two columns against each other: **the hash map wins everywhere except
order and memory.** So the moment a problem cares about order or about O(1)
space, the hash map stops being the automatic answer. That's the whole trade.

---

## 5. The patterns

This is the part that transfers. Each pattern is **a trigger in the problem
statement → the tool it should summon.** Learn the triggers, not the code.

### Pattern A — the seen-set
> **Trigger:** "does it contain a duplicate", "have we visited this", "is it unique"

Walk the input once, keeping a set of what you've already passed. Before
processing an element, ask the set. This is the plainest possible version of
*spend memory, buy time*: O(n²) nested loops collapse to O(n).

### Pattern B — the frequency map
> **Trigger:** "anagram", "how many times", "most common", "same characters"

Count occurrences into a map of `value → count`. Two things are anagrams exactly
when their frequency maps are equal. Once you have counts, questions like "which
appears most" become questions about the map, not the array.

### Pattern C — the complement lookup
> **Trigger:** "two numbers that sum to a target", "find a pair such that…"

The insight that trips people up: don't search for *pairs*, search for the
*partner*. Scanning at value `x` with target `t`, the partner you need is
`t - x` — a single, exact value. So a hash map answers "have I already passed
`t - x`?" in O(1). Build the map **as you scan**, not before: that's what stops
an element pairing with itself.

*(Note the contrast with [[../concepts/two-pointers|two pointers]], which solves
the same shape in O(1) space — but only if the array is sorted. Sorted → two
pointers. Unsorted → hash map. That fork comes back constantly.)*

### Pattern D — the canonical key
> **Trigger:** "group the ones that are equivalent", where *equivalent* isn't *equal*

Two strings are anagrams without being equal. So invent a **canonical form** — a
transformation that maps every member of a group to the identical key. Sorting
the characters works: `"eat"` and `"tea"` both become `"aet"`. Then a
`map[key] → list of members` groups them in one pass. Choosing the canonical
form *is* the problem; the grouping is bookkeeping.

### Pattern E — prefix / suffix accumulation
> **Trigger:** "for every position, something about everything *else*", "without division", "product/sum of the rest"

The naive answer recomputes the whole rest of the array at every index: O(n²).
Instead, sweep left-to-right accumulating everything before each index, then
right-to-left accumulating everything after, and combine. Two passes, O(n).

```
nums:      [ 2,  3,  4,  5 ]
prefix:    [ 1,  2,  6, 24 ]   ← product of everything to the LEFT
suffix:    [60, 20,  5,  1 ]   ← product of everything to the RIGHT
result:    [60, 40, 30, 24 ]   ← prefix[i] × suffix[i]
```

Notice the answer at index `i` never involves `nums[i]` itself. That's the trick.

### Pattern F — the set as O(1) neighbour test
> **Trigger:** "longest consecutive run", where the input is unordered and sorting is too slow

Drop everything into a set. Now "does `x+1` exist?" is O(1), so you can walk a
sequence forward without sorting. The second half of the trick is only starting
a walk from a number that *begins* a run (i.e. `x-1` is not in the set) — that's
what keeps the total work O(n) instead of O(n²).

---

## 6. Python notes

Pick one language and know it cold; if you don't have a strong preference,
Python is the pragmatic interview choice — least syntax between you and the idea.

```python
from collections import Counter, defaultdict

seen = set()                      # membership; add(), in, discard()
counts = Counter(nums)            # frequency map in one line
counts.most_common(2)             # [(value, count), ...] — but it SORTS: O(n log n)
groups = defaultdict(list)        # no KeyError; groups[k].append(v) just works
groups[tuple(my_list)].append(x)  # tuple() makes a list hashable

for i, v in enumerate(nums):      # index AND value — use this, not range(len(...))
    ...
```

**The one performance fact that matters most:**

```python
if x in my_list:   # O(n)  — a hidden loop over every element
if x in my_set:    # O(1)  — one hash computation
```

Identical syntax, completely different cost. Writing `in` against a list inside
a loop is the single most common way people accidentally write O(n²) and think
they wrote O(n). Skim the
[Python complexity cheat sheet](https://www.geeksforgeeks.org/complexity-cheat-sheet-for-python-operations/)
so you know which built-ins are secretly loops.

---

## 7. Traps

- **`in` on a list.** See above. It'll cost you a "make it faster" follow-up.
- **Claiming O(1) with no caveat.** Say "O(1) average, O(n) worst case if every
  key collides." Interviewers notice.
- **Forgetting the space cost.** If you built a hash map over the input, your
  solution is O(n) *space*. State it. Some problems then ask you to remove it.
- **Building the map before the scan in Two Sum.** Element pairs with itself.
- **Mutating a key.** Lists can't be keys; `tuple()` them.
- **`sorted()` sneaking in.** It's O(n log n). Fine if you intend it — fatal if
  you thought your solution was O(n).
- **Not handling empty input.** `[]`, `[""]`, single elements, all-identical
  elements. Check these *before* you say "I'm done".

---

## 8. Worked example

Nothing from the problem set, so nothing is spoiled.

> **Problem.** Given two integer arrays, return their intersection — the values
> appearing in both, each value once. `[1,2,2,1]` and `[2,2]` → `[2]`.

**Brute force.** For each element of `a`, scan `b`. O(n × m). Always say the
brute force out loud in an interview first — it proves you understood the
problem, and it gives you a baseline to improve on.

**Spot the pattern.** The inner scan is answering "is this value in `b`?" —
repeated membership testing. That's **Pattern A**. Membership testing is what a
set is for.

```python
def intersection(a, b):
    b_set = set(b)                     # O(m) time, O(m) space
    result = set()
    for x in a:                        # O(n)
        if x in b_set:                 # O(1) average  ← the whole win
            result.add(x)              # a set, so duplicates collapse for free
    return list(result)
```

**Complexity.** O(n + m) time, O(m) space. We spent memory on `b_set` and bought
back a factor of m in time.

**The move to internalise:** the brute force had a nested loop whose only job was
*searching*. Any time you see that, ask whether a hash structure can answer that
search in O(1). That question alone solves a large fraction of Arrays & Hashing.

---

## 9. Materials

Full list on [[../meta/resources|Resources]]. For this lesson, in order:

1. [Introduction to hashing](https://www.geeksforgeeks.org/introduction-to-hashing-2/) — 10 min
2. [Load factor & rehashing](https://www.geeksforgeeks.org/load-factor-and-rehashing/) — 10 min, the one that makes O(1) defensible
3. [Python complexity cheat sheet](https://www.geeksforgeeks.org/complexity-cheat-sheet-for-python-operations/) — skim, then keep it open
4. [NeetCode on YouTube](https://www.youtube.com/@NeetCode) — but **only after** a timed attempt on a given problem
5. Optional: [LeetCode 75](https://leetcode.com/studyplan/leetcode-75/) for extra reps if a pattern won't stick

---

## 10. Self-check

Answer these **aloud**, in full sentences, before starting the timer. Speaking
them is the point — it's the same muscle the interview uses. Any that come out
mumbled, go back to section 3.

1. Why is array indexing O(1) but array search O(n)?
2. What does a hash function actually do, in one sentence?
3. Why is a hash lookup "O(1) average" rather than just "O(1)"?
4. What's a load factor, and what happens when it's exceeded?
5. When is a hash map the *wrong* choice?
6. Why can't a Python list be a dictionary key? What do you do about it?
7. What's the cost of `x in my_list` versus `x in my_set`?
8. Give the trigger phrase for each of patterns A–F.

---

## 11. Ready?

**First timed problem: Contains Duplicate** (Easy) — Pattern A.

Say `timed contains-duplicate` and I'll give you the statement and start the
30-minute box. No hints while it's running.

**What "good" looks like here** — this one is short, so the bar isn't just
finishing:

- You state the brute force and its complexity *before* writing the fast version
- You reach for the set without needing to think about it
- You state both time **and** space complexity, unprompted, with the "average
  case" caveat
- You test `[]` and `[1]` before declaring done
- Clean names — `seen`, not `s`

If you finish well inside 30 minutes, don't stop the clock — roll straight into
**Valid Anagram** and use what's left. Two Easies in one box is a good session.

Not finishing is fine and expected on the Mediums later. It goes in the
[[../meta/review-queue|review queue]] and comes back around. **The plan is to
repeat until it's cold-solvable, not to clear the list once.**
