---
type: concept
status: learning
updated: 2026-09-13
sources: []
tags: [complexity]
---

# Constraint-Bounded Complexity

**In one sentence:** When a problem's constraints cap how many *distinct* things
can exist, a structure holding those things costs O(1), not O(n) — the
constraints block is part of the problem, not decoration.

## The mistake it prevents

The reflex is: *I built a hash map over the input, therefore O(n) space.*

That reflex is wrong whenever the map's **keys** are drawn from a bounded set.
The size of a map is bounded by the number of distinct keys it can hold, which
is not always the size of the input.

> `s` consists of lowercase English letters

26 possible keys. The map holds at most 26 entries for an input of 5·10⁴ or
5·10⁹. Space does not grow with `n`. **O(1).**

## How to say it

Not just "O(1)" — say where it comes from:

> "O(k), where k is the alphabet size, which the constraints bound at 26, so
> O(1)."

That phrasing survives the follow-up. The bare "O(1)" doesn't.

## The follow-up it sets up

*"What if the input were Unicode?"* — `k` is now unbounded, so the honest answer
becomes **O(min(n, k))**. Note which solutions survive that change: a hash map
does, a fixed 26-slot array does not. Interviewers ask this precisely to find out
whether the O(1) was analysed or recited.

## Where it recurs

Anywhere the key space is bounded by something other than input size: a fixed
alphabet, a fixed number of digits, a fixed board size, "at most 10 colours",
counts capped by the problem. Expect it again in Group Anagrams and in the
sliding-window string problems.

## Related

- [[frequency-map]] — where this usually bites
- [[hash-map]] · [[seen-set]]

## Evidence

- [[../problems/valid-anagram|Valid Anagram]], 2026-09-13 — space called O(n)
  when it was O(1). `learning`: the idea is understood, not yet demonstrated
  unprompted.
