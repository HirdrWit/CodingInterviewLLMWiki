---
type: problem
updated: 2026-09-13
sources: [https://leetcode.com/problems/contains-duplicate/]
tags: [arrays-hashing, easy, seen-set]
---

# Contains Duplicate

**Source:** [LeetCode 217](https://leetcode.com/problems/contains-duplicate/) ·
**Difficulty:** Easy · **Date:** 2026-09-13 · **Time taken:** 7 min of a 30 min box

First timed attempt on record. Topic 1, problem 1 of
[[../curriculum/arrays-hashing|Arrays & Hashing]].

## Statement

Given an integer array, return `true` if any value appears at least twice, and
`false` if every element is distinct. `1 <= len <= 10^5`, values in ±10^9.

## Result

`solved cold` — unaided, no hints, no lookups, correct on first write.

## Concepts used

[[../concepts/seen-set|Seen-Set]] (Pattern A) · [[../concepts/hash-map|Hash Map]]

## My approach

Went straight to hash-based membership. Never considered the nested loop — the
instinct from [[../lessons/01-arrays-hashing|Lesson 1]] was already there.

Scan once; at each element ask whether it has been seen before recording it;
return `True` immediately on a repeat; `False` after the loop.

## Wrong turns

None in the algorithm. Two things went sideways anyway:

**1. Built a map where a set belonged.** Used `result = dict()` with
`result[n] = 1`. The `1` is never read anywhere in the function.

The tell, and the transferable bit: **a dict whose values you never read is a
set.** Inventing a placeholder value to satisfy a data structure means the data
structure is wrong. Not a complexity difference — dict and set are the same
machine — but the structure chosen is a statement to the reader about what the
problem was understood to need.

**2. Named the seen-set `result`.** It isn't the result; the result is a
boolean. It's the set of things already passed. `seen`.

## The insight

Nothing unlocked, because nothing was locked — this problem's job is to make
the *habits* automatic, not to be hard. The habit that was missing is the
narration, not the algorithm.

## Solution

```python
class Solution:
    def containsDuplicate(self, nums: List[int]) -> bool:
        result = dict()
        for n in nums:
            if n in result:
                return True
            result[n] = 1
        return False
```

As it should have read:

```python
class Solution:
    def containsDuplicate(self, nums: List[int]) -> bool:
        seen = set()
        for num in nums:
            if num in seen:
                return True
            seen.add(num)
        return False
```

## Complexity

**Time O(n) average, O(n²) worst case. Space O(n).**

Stated at debrief as "time worst = O(n), space worst = O(n)". Space right; time
was the *average* labelled as the worst. n iterations × O(1)-average lookup
gives O(n); if every key collides into one bucket each lookup degrades to a
scan of up to n entries, so the true worst case is O(n²). See
[[../lessons/01-arrays-hashing|Lesson 1]] §3 and §7.

Rule adopted: **the words "on average" follow "O(1)" about a hash operation in
the same breath, every time, until it is involuntary.**

## Three fixes before the re-attempt

1. `seen = set()` — reach for the set when the values are never read.
2. State the brute force and *both* complexities aloud, unprompted, before
   writing the fast version. 7 minutes of silent correctness scores below
   narrated correctness in a real screen.
3. Say the empty-input case out loud even when the constraints rule it out.

## Process notes

Solved in 7 minutes and stopped, leaving 23 minutes of box unused — the lesson's
ready-check said to roll straight into the next Easy. Box time is scarce.

Worked in the LeetCode web editor deliberately, to build tolerance for an
unfamiliar editor with no autocomplete and no local test runner. Good call; keep
doing it.

## Re-attempt

Scheduled in [[../meta/review-queue|review queue]] for: **2026-09-16** (+3d,
clean solve). Pass condition is not just working code — it is `seen = set()`,
an explicit return on every path, and both complexities stated correctly and
unprompted.
