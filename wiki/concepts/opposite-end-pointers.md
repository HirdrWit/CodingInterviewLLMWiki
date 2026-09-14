---
type: concept
status: unseen
updated: 2026-09-14
sources: [https://www.geeksforgeeks.org/two-pointers-technique/]
tags: [two-pointers, algorithms]
---

# Opposite-End Pointers

**In one sentence, in my own words** — one index at each end of a sequence,
walking inward under a rule that makes each step throw away a whole family of
candidates, so a search over all n² pairs finishes in n steps.

Pattern A in [[../lessons/02-two-pointers|Lesson 2]]. The converging half of
[[two-pointers]]; the same-direction half is sliding window, which is a
different tool with a different trigger.

## When to reach for it

Trigger phrases: *"palindrome"*, *"reverse"*, *"from both ends"*, *"a pair such
that…"*, *"is it symmetric"*, *"maximum area between two lines"*.

Generally: the brute force is "check every pair", **and** the input has enough
structure — sortedness, symmetry — that looking at the two current ends tells
you which end is hopeless.

The test that decides it: **can I name a quantity that moves monotonically as
each pointer moves?** Sum of the pair (sorted array), width (always shrinks),
absolute value (sorted array). If no such quantity exists, this tool is not just
slow here, it's *invalid* — a move would discard a candidate that could still
have been the answer.

## How it works

```python
lo, hi = 0, len(seq) - 1
while lo < hi:
    # 1. evaluate the current pair
    # 2. decide which pointer is hopeless
    # 3. move exactly that one (or both, for an equality check)
    ...
```

Every instance answers two questions, and the second one is the whole problem:

- **What is compared at the ends?** Equality, a sum, an area.
- **Which pointer moves, and on what evidence?** Palindrome: both, on a match.
  Pair sum: exactly one, chosen by which way the sum must go. Max area: the one
  at the smaller value, because it can't improve.

**Filtering variant.** When some elements don't count (punctuation, whitespace),
an inner `while` advances past them. Still O(n) overall — the inner loops only
advance the same two pointers the outer loop advances, so total movement is
bounded by n. **Guard the inner loop with `lo < hi`** or an input made entirely
of skippable characters runs the pointer off the end.

## Minimal example

```python
def is_palindrome_simple(s):
    lo, hi = 0, len(s) - 1
    while lo < hi:
        if s[lo] != s[hi]:
            return False
        lo += 1
        hi -= 1
    return True
```

Note `lo < hi`, not `<=`: in an odd-length string the middle character is
trivially equal to itself, so there is nothing to check there.

## Complexity

**O(n) time.** Not O(n²), despite the loop shape. The argument, which should be
said out loud rather than asserted: *the gap `hi - lo` starts at n, strictly
decreases on every iteration, and the loop ends when it reaches zero — so at most
n iterations.*

**O(1) space.** Two integers. This is the entire reason the technique exists:
it's the answer to "now do it without the hash map."

If the input must be sorted first, the sort dominates: O(n log n) time, and the
space claim becomes "O(1) beyond the sort" (`list.sort()` in place, not
`sorted()`, which allocates).

## Gotchas

*This section grows every time I get it wrong. That's its job.*

- `lo < hi` vs `lo <= hi`. Pair problems want `<` — two distinct positions.
  Consuming-every-element problems (like merging into an output array) want the
  loop to run while `lo <= hi`. Decide deliberately; getting it wrong usually
  doesn't crash, it silently returns a wrong answer on one edge case.
- Moving both pointers when only one should move — skips candidate pairs.
- Unguarded inner skip loops → IndexError on all-skippable input.
- Slicing instead of moving indices. `s[lo:hi]` copies, turning O(n) into O(n²)
  time and O(1) into O(n) space while still *looking* like two pointers.
- Applying it to unsorted input because the code compiles.
- Empty and single-element inputs: `[]`, `[x]`, `""`.

## Related

- [[two-pointers]] — the parent idea. This page is its converging flavour;
  sliding window is the same-direction flavour, where the interest is the *range
  between* the pointers, not the pair *at* them.
- [[sort-then-scan]] — how you get the sortedness this technique needs when the
  input doesn't arrive with it.
- [[duplicate-skipping]] — the correctness bolt-on required once this runs
  inside an outer loop over triples.
- [[complement-lookup]] — solves the same pair-sum shape in O(n) time but O(n)
  space, and works on *unsorted* input. The fork: sorted → this; unsorted, or
  indices required → complement lookup.
- [[seen-set]] — the Lesson 1 tool this one is the O(1)-space alternative to.
- [[../skills/algorithms|Algorithms]] — parent skill.

## Evidence

Problems where I used this unaided:

*None yet. Add links here — and only then promote the status.*
