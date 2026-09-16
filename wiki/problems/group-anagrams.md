---
type: problem
updated: 2026-09-16
sources: [https://leetcode.com/problems/group-anagrams/]
tags: [arrays-hashing, medium, canonical-key]
---

# Group Anagrams

**Source:** [LeetCode 49](https://leetcode.com/problems/group-anagrams/) ·
**Difficulty:** Medium · **Date:** 2026-09-16 · **Time taken:** 29 min 42 s of a
30 min box

Topic 1, problem 4 of [[../curriculum/arrays-hashing|Arrays & Hashing]].
**First Medium on record.**

## Statement

Group the anagrams in an array of strings, in any order.
`1 <= len(strs) <= 10^4`, `0 <= len(strs[i]) <= 100`, lowercase only.
Note `0` — the empty string is a legal input and appears in the examples.

## Result

`solved cold` — unaided, no hints, no lookups, **correct on first write**.

## Concepts used

[[../concepts/canonical-key|Canonical Key]] (Pattern D) ·
[[../concepts/hash-map|Hash Map]]

## My approach

Canonical form as the dict key. Anagrams are exactly the strings that are equal
once sorted, so `sorted(s)` identifies a group. Convert to a tuple to make it
hashable, append each string to its bucket, return the values.

Brute force stated up front, unprompted: nested loop comparing every string
against every other, O(n²), rejected.

## Wrong turns

**None in the algorithm.** The canonical-key idea was reached immediately, and
`tuple(...)` — needed because a list is unhashable — went in without hesitation.
That detail stalls people mid-box and did not stall here.

The misses were in the analysis and the code shape.

## The insight

**Grouping problems are keyed on a canonical form.** The question to ask is "what
do all members of a group share, exactly?" — then make that the key. Here it is
the multiset of letters. Sorting is one way to canonicalise it; a 26-length count
tuple is another, and cheaper (see below).

## Solution

As written, in the box:

```python
class Solution:
    def groupAnagrams(self, strs: list[str]) -> list[list[str]]:
        store = dict()
        for s in strs:
            sorted_str = sorted(s)
            if tuple(sorted_str) in store:
                store[tuple(sorted_str)].append(s)
                continue
            store[tuple(sorted_str)] = [s]

        result = []
        for _, value in store.items():
            result.append(value)

        return result
```

As it should read:

```python
class Solution:
    def groupAnagrams(self, strs: list[str]) -> list[list[str]]:
        store = defaultdict(list)
        for s in strs:
            store[tuple(sorted(s))].append(s)
        return list(store.values())
```

Three separate things collapsed there:

1. `tuple(sorted_str)` was computed **three times**. Hoist it, or inline it once.
2. The check-then-insert shape is `defaultdict(list)`, or
   `store.setdefault(key, []).append(s)` if the import isn't wanted. **Third
   appearance of this same shape** — see [[contains-duplicate]] and
   [[valid-anagram]].
3. `for _, value in store.items()` asks for pairs and discards half of each.
   `list(store.values())`.

Minor: `sorted_str` holds a list, not a string.

## Complexity

**Time O(n · k log k). Space O(n · k).**
`n` = number of strings (≤10⁴), `k` = max string length (≤100).

Stated in the box as **"O(n log n) for use of the sorted function"** — and that
is the one real error of this box, not a phrasing tic.

`sorted` here runs over a **string of length k**, not over the array. Each sort
is O(k log k); there are n of them. `O(n log n)` describes an algorithm that
sorts the array, which this is not. Two parameters were flagged before the box
started and the answer still came back with one.

Space is O(n · k) for the same reason: every string is stored, plus its key.

**The follow-up to be ready for — can the log factor go?** Yes. Key on a
26-length tuple of letter counts instead of sorted letters. Counting is O(k), so
the whole thing is **O(n · k)**. This is the better solution and the natural
answer to "what else could you do here". It also connects back to
[[valid-anagram]]: the bounded alphabet is what makes the count key fixed-width.

**What was right:** *"dict lookup average O(1), O(n) worst case since endless
possibilities of words as keys, not just letters."* That is the key-space
reasoning done correctly, and attached to the **lookup** rather than to the
total — precisely the distinction missed on [[contains-duplicate]] the same
morning. Unbounded key space, so collisions are not capped. See
[[../concepts/constraint-bounded-complexity]].

## Drills

Two of three fired, both for the first time. See [[../meta/drills|drills]].

| Drill | Result |
|---|---|
| D1 — name what you rejected | **pass**, 1 of 2. Thin on the *why* — "rejected for solution above" states the choice, not the reason — but it fired unprompted |
| D2 — hash worst case from the key space | **pass**, 1 of 3 |
| D3 — name the stdlib tool | **fail.** `defaultdict` / `setdefault` not named. Only drill yet to fire |

## Process notes

**Used 29 min 42 s of the 30 — roughly 4× an Easy.** Worth recording plainly: the
first Medium consumed the whole box where Easies were taking 4–7 minutes. It was
finished and correct, so the ceiling is above this problem, but the margin is
gone. That is the first real information about pace the vault has.

Narration held for a **3rd consecutive box** under Medium pressure — the
condition that gap needed to close.

## Fixes before the re-attempt

1. **Two parameters, not one.** When a problem has both a count of items and a
   size per item, name both before writing complexity.
2. `defaultdict(list)` — the check-then-insert shape, third time.
3. Compute a key **once**, into a named variable.

## Re-attempt

Scheduled in [[../meta/review-queue|review queue]] for **2026-09-19** (+3d).
Pass condition: solved cold again, `defaultdict` or `setdefault` used or named,
and complexity given in **both** `n` and `k`.
