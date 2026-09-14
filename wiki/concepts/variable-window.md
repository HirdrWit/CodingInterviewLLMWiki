---
type: concept
status: unseen
updated: 2026-09-14
sources: []
tags: [sliding-window, algorithms]
---

# Variable Window

**In one sentence, in my own words:** A [[sliding-window|sliding window]] whose
width isn't given but is decided as you go — the right edge always advances, and
the left edge advances only when a condition tells it to.

Introduced in [[../lessons/03-sliding-window|Lesson 3]]. The fixed-size window is
bookkeeping; this is where the actual thinking is.

## When to reach for it

Trigger: **"longest"** or **"shortest"** contiguous stretch satisfying some
property — as opposed to "every window of size k", which is the fixed case.

Also: "at most k of X", "containing all of Y", "without any repeated Z". Each of
those is a property the window either has or doesn't, with no size stated.

## How it works

Three slots, always in this order:

```python
left = 0
for right in range(len(s)):
    # 1. ADMIT   s[right] into the window; update state
    while <condition>:
        # 2. EVICT  s[left]; undo its effect on state
        left += 1
    # 3. RECORD  the answer from the current window
```

The problem only ever changes what goes in the three slots. The condition in the
`while` is the design decision — see [[window-invariant]] for the two shapes it
takes and where the RECORD goes in each.

**Admit and evict are mirror images.** Write them as a visual pair; a missing
undo is then obvious rather than invisible.

## Minimal example

Longest run containing at most 2 distinct characters:

```python
from collections import defaultdict

def longest_at_most_2_distinct(s):
    counts = defaultdict(int)
    left = best = 0
    for right, c in enumerate(s):
        counts[c] += 1                    # admit
        while len(counts) > 2:            # invalid → shrink
            out = s[left]
            counts[out] -= 1              # evict: undo
            if counts[out] == 0:
                del counts[out]           # so len(counts) means "distinct in window"
            left += 1
        best = max(best, right - left + 1)   # record AFTER — this is a "longest"
    return best
```

The `del` matters: without it `len(counts)` counts characters that have already
left the window, and the invariant silently means the wrong thing.

## Complexity

O(n) time. The nested loop is not a multiplier — `left` and `right` each advance
at most n times *in total* across the whole run and never backtrack, so total
pointer movement is bounded by 2n. That's an **amortized** argument, and saying
the word out loud is worth credit when the interviewer squints at the `while`
inside the `for`.

Space is the state: O(k) for a map over the window, O(1) when the alphabet is
bounded.

## Gotchas

- **`if` instead of `while`.** One eviction is often not enough — after an admit
  you may need several. An `if` leaves the window invalid and the bug is quiet.
- **Recording in the wrong place.** Longest → after the loop; shortest → inside
  it. See [[window-invariant]].
- **Stale keys in the state.** A count that reaches zero must be removed if
  `len(state)` is what the condition reads.
- **`left` overtaking `right`.** Guard it, and be clear about whether an empty
  window is legal in your formulation.

## Related

- [[sliding-window]] — the parent idea; this is the non-fixed-width case
- [[window-invariant]] — the condition that governs slot 2, and the fork that
  decides slot 3
- [[two-pointers]] — same two indices, opposite geometry: converging from the
  ends, interested in the pair rather than the range
- [[seen-set]], [[frequency-map]] — the usual window state

## Evidence

Problems where I used this unaided:

*None yet. Add links here — and only then promote the status.*
