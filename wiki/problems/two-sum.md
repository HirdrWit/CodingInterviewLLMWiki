---
type: problem
updated: 2026-09-14
sources: [https://leetcode.com/problems/two-sum/]
tags: [arrays-hashing, easy, complement-lookup]
---

# Two Sum

**Source:** [LeetCode 1](https://leetcode.com/problems/two-sum/) ·
**Difficulty:** Easy · **Date:** 2026-09-14 · **Time taken:** 11 min of a 30 min box

Topic 1, problem 3 of [[../curriculum/arrays-hashing|Arrays & Hashing]].
**The box where the process finally happened.**

## Statement

Given `nums` and `target`, return the **indices** of the two numbers summing to
`target`. Exactly one solution; an element may not be used twice.
`2 <= len <= 10^4`, values and target in ±10^9.

That it returns *indices* rather than values is the detail that shapes
everything — see the rejected alternative below.

## Result

`solved cold` — unaided, correct first write, brute force narrated up front,
complexity volunteered with the average-case caveat.

## Concepts used

[[../concepts/complement-lookup|Complement Lookup]] (Pattern C) ·
[[../concepts/constraint-bounded-complexity|Constraint-Bounded Complexity]]

## My approach

One pass. At each element compute the partner needed to reach the target, ask
the map whether that partner has already been passed, and only then record the
current element.

## Wrong turns

**None.** First box with no correctness issue, no structural issue, and no
missing process step beyond naming the rejected alternative.

## The insight

**Don't search for a pair — search for the partner.** At value `x`, the thing
needed is exactly `target - x`: one specific number, not a range. That converts
"find a combination" (inherently a nested loop) into "have I seen this one
value?" (a single O(1) question).

The second half, and the reason the ordering matters:

```python
if complement in seen:   # check
    ...
seen[num] = i            # THEN insert
```

Reversed, `nums = [3, 5], target = 6` returns `[0, 0]` — index 0 pairing with
itself, because `3` was already in the map when its partner was requested.
Checking before inserting makes self-pairing **structurally impossible** rather
than something guarded against afterwards.

## Solution

As written in the box:

```python
class Solution:
    # Worst case, o(n^2), check every number to find the pair in a double
    # nested for loop. O(1) space.
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        store = dict()
        for i, num in enumerate(nums):
            val = target - num
            if val in store:
                return [store.get(val), i]
            store[num] = i
        return []
```

Correct. Cleaned up — same algorithm, better names:

```python
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        seen = {}
        for i, num in enumerate(nums):
            complement = target - num
            if complement in seen:
                return [seen[complement], i]
            seen[num] = i
        return []
```

`val` was the weakest name in the file — **the name *is* the insight.**
`complement = target - num` reads as an explanation; `val = target - num` reads
as arithmetic. Also `store` → `seen`, and `store.get(val)` → `seen[complement]`
(inside `if complement in seen` the key is guaranteed present; `.get()` falsely
signals it might not be).

The explicit `return []` is unreachable under "exactly one valid answer exists"
and is worth keeping anyway — it satisfies the return type on every path. Say
so if asked, rather than looking like it was left in by accident.

Nit: the comment says `o(n^2)`. Little-o means *strictly* slower-growing and so
excludes n² itself. Capital O. Nobody fails you for it, but whiteboard photos
are forever.

## Complexity

**Time O(n) average, O(n²) worst case. Space O(n).**

Stated at debrief as "average O(n) / O(n)" — the caveat present for the first
time after two consecutive misses. Still only half the sentence: *say the worst
case too.* "Average" on its own leaves the interviewer to wonder whether the
word is understood or decorative.

### Why O(n) space here but O(1) in Valid Anagram

The question [[valid-anagram|yesterday's problem]] sets up, asked in reverse.

Both build a hash map over the input; both are O(k) in distinct keys. **Space is
O(min(n, k)).**

| | k | n | binds | space |
|---|---|---|---|---|
| [[valid-anagram\|Valid Anagram]] | 26 (lowercase letters) | ≤ 5·10⁴ | k | **O(1)** |
| Two Sum | ≈ 2·10⁹ (±10⁹) | ≤ 10⁴ | n | **O(n)** |

The bound in Two Sum exists but is useless — astronomically larger than `n`, so
`n` is the binding constraint. One formula, two answers, and which side you are
on is a fact about the **constraints**, not about the data structure.

## The rejected alternative — and why it's rejected

Sort, then two pointers from both ends: O(n log n) time, O(1) space.

It fails here for a **specific and interesting** reason: the problem returns
**indices**, and sorting destroys them. Pairing each value with its original
index before sorting puts the O(n) space straight back.

Worth thirty seconds out loud. It shows you read *what the problem returns*, not
just what it asks — and it's the sorted/unsorted fork from
[[../lessons/01-arrays-hashing|Lesson 1]] appearing with a twist, where the
usual rule doesn't apply. Knowing why a rule doesn't apply beats knowing the rule.

**This was the one process step still skipped.**

## Three fixes before the re-attempt

1. **Name the rejected alternative and why** — the last missing process step.
2. **Finish the complexity sentence** — average *and* worst, both spoken.
3. **`complement`, `seen`, `seen[k]`** — name things for what they are.

## Re-attempt

Scheduled in [[../meta/review-queue|review queue]] for: **2026-09-17** (+3d).
Pass condition: clean names, both complexities stated, and the sort/two-pointer
alternative named and rejected with the indices reason.
