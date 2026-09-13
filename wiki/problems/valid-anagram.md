---
type: problem
updated: 2026-09-13
sources: [https://leetcode.com/problems/valid-anagram/]
tags: [arrays-hashing, easy, frequency-map]
---

# Valid Anagram

**Source:** [LeetCode 242](https://leetcode.com/problems/valid-anagram/) ·
**Difficulty:** Easy · **Date:** 2026-09-13 · **Time taken:** 7 min of a 30 min box

Topic 1, problem 2 of [[../curriculum/arrays-hashing|Arrays & Hashing]].
Second timed box, same day as [[contains-duplicate|Contains Duplicate]].

## Statement

Given two strings `s` and `t`, return `true` if `t` is an anagram of `s` —
same letters, same counts, rearranged. `1 <= len <= 5*10^4`, **lowercase English
letters only**. That last clause turns out to be the whole problem.

## Result

`solved cold` — unaided, correct first write, complexity volunteered unprompted.

## Concepts used

[[../concepts/frequency-map|Frequency Map]] (Pattern B) ·
[[../concepts/constraint-bounded-complexity|Constraint-Bounded Complexity]]

## My approach

Build a `char -> count` dict for each string, compare the two dicts. Went
straight there; no time spent considering alternatives.

## Wrong turns

None in the algorithm. The misses were in analysis and structure.

**1. Called the space O(n). It is O(1).** The constraints say lowercase English
letters, so the dicts hold at most 26 entries regardless of input size. The
space is bounded by the *alphabet*, not by `n`. Generalised as
[[../concepts/constraint-bounded-complexity|Constraint-Bounded Complexity]].

**2. Wrote the same loop twice**, once per string, instead of extracting it.

**3. Skipped the narration step** — the brute force was asked for in writing
before the box and wasn't done. Produced at debrief instead, and it was
incomplete (see below), which is exactly the argument for doing it up front.

## The insight

**The constraints block is part of the problem statement, not decoration.**
"What's your space complexity?" on this problem is a filter: `O(n)` means
pattern-matching *I built a hash map, therefore O(n)*; `O(1)` means actually
looking at what can go in it.

Second insight, from the brute force: **checking existence is not checking
count.** "Does every char of `s` appear in `t`" passes `"aab"` vs `"abb"` —
that tests the same *set* of letters, not the same *multiset*. The brute force
needs each match consumed (found and removed) to be correct. Notable that the
O(n²) version is *harder to get right* than the O(n) one; the frequency map
gets the multiset bookkeeping for free.

## Solution

As written in the box:

```python
class Solution:
    def isAnagram(self, s: str, t: str) -> bool:
        dict_s = dict()
        dict_t = dict()

        for ch in s:
            if ch in dict_s:
                dict_s[ch] = dict_s[ch] + 1
                continue
            dict_s[ch] = 1

        for ch in t:
            if ch in dict_t:
                dict_t[ch] = dict_t[ch] + 1
                continue
            dict_t[ch] = 1

        return dict_s == dict_t
```

Correct. What it should have been:

```python
class Solution:
    def isAnagram(self, s: str, t: str) -> bool:
        if len(s) != len(t):
            return False

        def counts(word):
            freq = {}
            for ch in word:
                freq[ch] = freq.get(ch, 0) + 1
            return freq

        return counts(s) == counts(t)
```

And the stdlib version, which should be *named* even if the hand-rolled one is
written: `return Counter(s) == Counter(t)`.

The move to make in an interview: reach for `Counter`, then say *"that's doing
exactly what I'd hand-roll — want me to write it out?"* One-liner alone reads as
memorised; hand-rolling when a one-liner exists reads as not knowing the tools.

## What went right

**Compared the dicts directly.** `dict_s == dict_t` checks keys and values both
ways, sidestepping the usual bug — looping over one dict's keys and forgetting
the other may hold keys the first doesn't.

**Didn't special-case unequal lengths and didn't need to.** Different lengths
give different total counts, so the dicts differ. Correct by construction rather
than by patching. (The explicit `len` guard is still worth adding — O(1), and it
rejects a large class of inputs before allocating.)

## Complexity

**Time O(n) average, Space O(1).**

Stated at debrief as "O(n) time, O(n) space". Time correct but again without the
"on average" caveat — second consecutive miss. Space wrong: see above.

Also unmentioned: `dict_s == dict_t` is itself O(k) in the number of distinct
keys, not free. Bounded at 26 here, but operations should be noticed rather than
assumed atomic.

**The follow-up to be ready for:** *what if the strings were Unicode?* Then `k`
is unbounded, space becomes O(min(n, k)) honestly, and a fixed 26-slot array
solution breaks while the dict version doesn't.

**The alternative worth naming:** `sorted(s) == sorted(t)`, O(n log n). Mention
it in order to say why it was rejected — naming the road not taken, with the
reason, is cheap and reads as senior.

## Three fixes before the re-attempt

1. **Read the constraints into the complexity.** Bounded alphabet → O(1) space.
2. **Never write the same loop twice** — extract it, or use `Counter` and say
   what it's doing underneath.
3. **"On average" follows "O(1)" about a hash op.** Second miss.

## Re-attempt

Scheduled in [[../meta/review-queue|review queue]] for: **2026-09-16** (+3d).
Pass condition: deduplicated, `O(1)` space stated correctly and unprompted, and
`Counter` named.
