---
type: problem
updated: 2026-09-16
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

---

## Re-attempt 1 — 2026-09-16 (cold), 5 min 53 s

**Result: `fail`** on the pre-stated pass condition — 2 of its 3 clauses met.
Requeued +3d to **2026-09-19**.

```python
class Solution:
    def isAnagram(self, s: str, t: str) -> bool:
        if len(s) != len(t):
            return False

        def count(s: str) -> dict:
            counts = dict()
            for ch in s:
                if ch not in counts:
                    counts[ch] = 1
                    continue
                counts[ch] = counts[ch] + 1
            return counts

        return count(s) == count(t)
```

### What passed

- **Deduplicated.** Fix #2's first half landed — the loop is extracted into a
  helper instead of written twice. Came back from memory unprompted.
- **O(1) space, stated correctly and unprompted**, with the right justification:
  the alphabet is bounded at 26, so the dict cannot grow with `n`. This was the
  headline miss of 2026-09-13 and it is now fixed. See
  [[../concepts/constraint-bounded-complexity]].
- **Length guard as an early exit.** Not needed for correctness — dict equality
  catches it — but it is the right instinct and reads well.

### What failed: `Counter` never named

Fix #2 was *"extract it, **or use `Counter` and say what it is doing
underneath**"*. The extraction happened; the tool was never named. What got
written instead was four lines and a `continue` for one idea:

```python
if ch not in counts:
    counts[ch] = 1
    continue
counts[ch] = counts[ch] + 1
```

Every shorter form: `Counter(s) == Counter(t)` · `counts[ch] = counts.get(ch, 0) + 1`
· `defaultdict(int)`.

The interview move is to say both: *"this is `Counter`; I'll write it out to show
the mechanism."* Naming the library and then choosing to hand-roll reads as
fluent. Hand-rolling silently reads as not knowing it exists.

Minor: `def count(s)` shadows the outer `s`.

### The complexity — best analysis on record, and one step short

Stated, unprompted:

> "Average O(n) to parse, average O(1) for insert, worst O(n) for collisions.
> Space O(1) because 26 characters is finite."

First time the cost has been **decomposed** — per-element cost separated from the
loop, with average/worst attached to the hash operation rather than to the whole
algorithm. Three days ago the caveat was absent entirely; earlier the same day,
on [[contains-duplicate|Contains Duplicate]], it was attached to the wrong
number. This is the habit forming.

Two steps short:

**1. Never multiplied out to a total.** The components were right, the sentence
stopped before the answer.

**2. The space argument fixes the time argument — and the connection was
missed.** If the alphabet bounds the dict to ≤26 entries, then a collision scan
is over ≤26 entries, i.e. O(1). So:

> **Time is O(n) — worst case included.** Not O(n²).

This is the genuinely senior observation available on this problem, and both
halves of it were already in hand. Contrast with `contains-duplicate`, where
`k ≈ 2·10⁹` does *not* bind and the O(n²) worst case is real. **Same formula,
opposite answer, and which side you land on is a fact about the constraints.**

**3. Phrasing.** *"closer to O(1) than O(n)"* — it **is** O(1). Hedged complexity
reads as uncertainty even when the answer is right.

### Still missing: the rejected alternative — 0 across 5 boxes

Named in this very write-up on 2026-09-13 as "cheap and reads as senior", and
still not said:

> "Sorting both strings and comparing is O(n log n) time, O(1) extra space.
> I'm counting instead for O(n) time, and the alphabet bounds the space anyway."

### Next re-attempt

**2026-09-19** (+3d), same day as [[contains-duplicate|Contains Duplicate]].
Pass condition — the code is proven, so only the talking is under test:

1. **`Counter` named**, whether or not it is the version written.
2. Time given as a **single total**: O(n), worst case included, *because* the
   bounded alphabet caps collisions.
3. Space O(1), stated flat — no hedging.
4. **One rejected approach named with its reason.**
