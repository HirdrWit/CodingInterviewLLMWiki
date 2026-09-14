---
type: concept
status: unseen
updated: 2026-09-14
sources: [https://www.geeksforgeeks.org/window-sliding-technique/]
tags: [sliding-window, algorithms]
---

# Sliding Window

**In one sentence, in my own words:** Keep a contiguous range of the input
marked by two forward-moving indices, and maintain a summary of what's inside it
*by delta* — one element enters, one leaves — so that scanning every candidate
range costs O(n) instead of O(n²).

Introduced in [[../lessons/03-sliding-window|Lesson 3]].

## When to reach for it

The trigger. Three words in a problem statement, any of which should summon this:

- **"contiguous"** / **"subarray"** / **"substring"** — the selection must be an
  unbroken run, which is what makes a window meaningful
- **"longest"** or **"shortest"** attached to a condition
- a **fixed size k** ("every window of size k", "average of k consecutive")

And one negative trigger that's just as useful: if sorting the input would
destroy the problem, that rules out [[two-pointers]] and points here.

## How it works

The window is one object with two edges. The right edge admits; the left edge
evicts. The state is never rebuilt, only adjusted:

```
[ 2 , 1 , 5 , 1 , 3 ]
    ╔═══════╗           sum = 6
      ╔═══════╗         −1 +1  →  sum = 7      two ops, regardless of width
```

Two families:

- **Fixed window** — the width is given. Left moves in lockstep with right, one
  admit and one evict per step. No decision to make.
- **[[variable-window|Variable window]]** — the width is determined by a
  condition. The interesting one; the decision of *when to shrink* is the whole
  problem. See [[window-invariant]].

## Minimal example

Fixed window, the simplest case — maximum sum of any k consecutive elements:

```python
def max_sum_k(nums, k):
    total = sum(nums[:k])          # build the first window once
    best = total
    for right in range(k, len(nums)):
        total += nums[right]       # admit
        total -= nums[right - k]   # evict — always paired with the admit
        best = max(best, total)
    return best
```

Length of the inclusive window `nums[left..right]` is `right - left + 1`. Half
the bugs in this topic are that `+ 1`.

## Complexity

O(n) time: each index advances at most n times across the entire run and never
backtracks, and admit/evict are each O(1).

Space is whatever the state costs — O(1) for a running sum, O(k) for a
[[frequency-map]] or [[seen-set]] over the window's contents. When the alphabet
is bounded (26 lowercase letters), that's O(1), not O(n) — see
[[constraint-bounded-complexity]].

**The O(1) admit/evict is load-bearing.** If updating the state costs O(k) —
re-slicing the string, rebuilding a set, calling `max()` over the counts — the
whole thing quietly becomes O(n·k) while still looking like a sliding window.

## Gotchas

*Grows every time I get this wrong. That's its job.*

- Recomputing the window instead of updating it by delta. The commonest way to
  write a fake sliding window.
- `left += 1` without undoing that element's effect on the state.
- Slicing (`s[left:right+1]`) inside the loop — it's an O(k) copy.
- Sum-based conditions break on **negative numbers**: growing the window no
  longer only increases the sum, so the greedy shrink is unsound. Prefix sums +
  a hash map is the usual replacement.

## Related

- [[two-pointers]] — the same two indices, but converging from the ends and
  interested in the *pair* they point at, not the range between them. Two
  pointers wants sorted input; sliding window usually forbids sorting.
- [[variable-window]] — the sub-case where the width is decided by a condition
  rather than given.
- [[window-invariant]] — the property that decides when the left edge moves.
- [[frequency-map]], [[seen-set]] — what the window's state usually is.

## Evidence

Problems where I used this unaided:

*None yet. Add links here — and only then promote the status.*
