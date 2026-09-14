---
type: concept
status: unseen
updated: 2026-09-14
sources: [https://www.geeksforgeeks.org/merging-intervals/]
tags: [intervals, sorting]
---

# Sweep Line

**In one sentence, in my own words** — stop treating an interval as an object:
cut each one into a `+1` start event and a `-1` end event, throw away which
interval they came from, sort all `2n` events by position, and walk them keeping
a running counter — the counter is how many things are active where you're
standing, and its running maximum is the peak concurrency.

Pattern E in [[../lessons/16-intervals|Lesson 16 — Intervals]].

## When to reach for it

The word to listen for is **concurrent**.

Trigger: "minimum number of rooms / resources / machines", "maximum concurrent",
"how many at once", "peak load", "does it ever exceed capacity".

The underlying signature: the question is about a **quantity at a point on the
axis**, and that quantity only changes at an interval's boundaries. Nothing
happens in the middle of an interval, so only the `2n` boundaries need visiting
— that's the whole compression.

It generalises past ±1 in a way the alternatives don't: if events carry a
**weight** (passengers boarding, bandwidth reserved, cost incurred) the sweep
absorbs it with no change of shape — `+num` and `-num` instead of `+1` and `-1`.
That's the reason to reach for it over a heap.

Anti-trigger: if you need to know *which* intervals are the concurrent ones, the
sweep has discarded exactly that. Use a heap.

## How it works

Three steps.

1. **Decompose.** Each `[start, end]` becomes two events.
2. **Sort** all `2n` events by position, with a deliberate tie-break.
3. **Scan**, adding each delta to a counter and tracking the counter's maximum.

```
[2, 6]   →   (2, +1)  "one more thing is now active"
             (6, -1)  "one fewer thing is active"
```

```
intervals: [0,4] [1,3] [2,5]

events:  (0,+1) (1,+1) (2,+1) (3,-1) (4,-1) (5,-1)

counter:    1      2      3      2      1      0
                          ↑
                    max = 3   ← the answer to "how many rooms?"
```

You have thrown away which interval is which, and in exchange the problem is now
"scan a sorted list of ±1s and track a max." **That trade is the sweep line.**

### The tie-break is where the correctness lives

When a start and an end land on the same position, which is processed first?

- **Exclusive-end semantics** (one meeting ends at 3, another starts at 3, no
  conflict): process the **end first**, or the counter briefly reads one too
  high and you over-report the peak.
- **Inclusive semantics** (they do conflict): process the start first.

Encode it in the **sort key**, not in an `if`:

```python
events = []
for start, end in intervals:
    events.append((start, 1))
    events.append((end, -1))
events.sort()      # (position, delta); -1 sorts before +1 at equal position,
                   # so ends are processed first → exclusive-end semantics
```

A bare `events.sort()` gets the exclusive-end tie-break right **by accident of
`-1 < 1`**. It's the right behaviour, but say out loud why it works — don't let
the interviewer think you got lucky. For inclusive semantics you need the key to
put `+1` first instead.

The endpoint question from [[interval]] is what decides which you want, so ask it
before you write the sort.

### The heap alternative

For plain room-counting there's an equivalent: sort by start, walk, keep a
min-heap of the end times of currently-running intervals, popping every end
`<=` the new start before pushing your own. `len(heap)` is the rooms in use; its
max is the answer. See [[heap]] and [[two-heaps]] for the machinery.

Both are O(n log n). The heap version **tells a better story out loud** ("this
heap is the set of rooms currently occupied, ordered by who frees up first");
the sweep is fewer lines and handles weights. Know both, lead with whichever you
can narrate.

## Minimal example

Weighted, because that's where the sweep earns its place over a heap — does
occupancy ever exceed a capacity?

```python
def never_exceeds(trips, capacity):
    events = []
    for num, start, end in trips:
        events.append((start, num))     # +num board here
        events.append((end, -num))      # -num alight here

    events.sort()                       # at equal position, negatives first:
                                        # people get off before the next group on
    onboard = 0
    for _, delta in events:
        onboard += delta
        if onboard > capacity:
            return False
    return True
```

Same three steps, `±num` instead of `±1`, no other change. Swapping the counter
for a `max()` instead of a capacity check gives you peak concurrency directly.

## Complexity

**O(n log n) time** — building `2n` events is O(n), sorting them is O(n log n)
and dominates, the scan is O(n). `2n` is still `n log n`; don't quote the
constant.

**O(n) space** for the events list. This is a real cost, and the one place the
heap version can win: the heap holds only the *currently active* intervals,
which may be far fewer than `n`.

## Gotchas

- **Leaving the tie-break to chance.** Starts and ends at equal position must be
  ordered deliberately. Put it in the sort key, not in a branch — a branch
  inside the loop is where this gets written backwards.
- **Not asking inclusive vs exclusive first.** The tie-break is downstream of
  that answer; you cannot pick one without it.
- **Forgetting to track the max.** The final counter is always 0 — every start
  has a matching end. The answer is the *running* maximum, never the end state.
  This is the same `cur` vs `best` separation as in [[kadane]].
- **Updating the max before adding the delta.** Add first, then max.
- **Sorting events by position only, discarding the delta from the key.**
  Python's default tuple sort uses the delta as the tie-break for free; a
  `key=lambda e: e[0]` throws that away and makes the order unstable-looking.
- **Reaching for a sweep when you need to know *which* intervals overlap.** The
  decomposition destroyed that information on purpose.

## Related

- [[interval]] — the object being decomposed, and the endpoint question that
  decides the tie-break
- [[sort-by-start]] — the other family, for when intervals stay intact as
  objects; the sweep is what you use when neither sort key helps
- [[heap]] — the equivalent solution for unweighted concurrency; keeps the active
  set instead of throwing it away, so it costs less space and tells a better story
- [[kadane]] — unrelated problem, same discipline: a running value and a separate
  running maximum
- [[sort-then-scan]] — the umbrella shape

## Evidence

Problems where I used this unaided:
