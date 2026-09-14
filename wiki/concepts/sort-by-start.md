---
type: concept
status: unseen
updated: 2026-09-14
sources: [https://www.geeksforgeeks.org/merging-intervals/, https://www.geeksforgeeks.org/activity-selection-problem-greedy-algo-1/]
tags: [intervals, greedy, sorting]
---

# Sort by Start (vs Sort by End)

**In one sentence, in my own words** — in interval problems the loop is boring
and the sort key *is* the algorithm: sort by **start** when you're combining
overlapping pieces into bigger ones, sort by **end** when you're keeping as many
non-overlapping pieces as you can.

From [[../lessons/16-intervals|Lesson 16 — Intervals]], Patterns B, C and D.

## When to reach for it

The decision, in one table. This is the page worth having memorised:

| The statement says | Sort by | Because |
|---|---|---|
| "merge all overlapping", "combine", "total ground covered" | **start** | you're absorbing forwards |
| "can a person attend all of these", "is there any conflict" | **start** | adjacent pairs settle it |
| "minimum number to remove so none overlap" | **END** | finishing earliest leaves the most room |
| "maximum number you can attend / schedule" | **END** | same problem, counted the other way |
| "minimum rooms", "maximum concurrent", "peak" | neither — decouple | [[sweep-line]] |

The tell for the end-sorted family is the words **non-overlapping**, **remove
the fewest**, or **fit as many as possible**. The tell for the start-sorted
family is **merge** or **any conflict at all**.

## How it works

### Sorting is tilting the ruler

Unsorted intervals are tape thrown on the floor. Sorting exists for exactly one
reason: **to make "everything relevant is behind me" true**, which is the
property that makes one pass sufficient. Every argument below is a version of
that sentence.

### Sort by start → merge

Keep the last interval in your output as "current". For each next interval:
overlaps the current → absorb it (`current.end = max(current.end, next.end)`);
otherwise the current one is finished, so emit and start a new current.

```
sorted:   [1,3]  [2,6]  [8,10]  [15,18]

current:  [1,3] → absorb [2,6] → [1,6] → [8,10] doesn't touch → emit, new current
output:   [1,6]  [8,10]  [15,18]
```

**Why one pass is legal** — this is the proof sentence, and it's worth being able
to say:

> Once sorted by start, any interval that could overlap the current one must
> start at or after the current one's start. So it either overlaps the current
> interval, or it starts strictly after the current one ends — and if it starts
> after the current one ends, so does every interval behind it. Nothing already
> emitted can ever be revisited.

Because you sorted by start, the merged start is always just the current start —
`min` is free. The `max` on the end is not; see [[interval]] for the containment
bug that follows from dropping it.

The same sort also settles "can a person attend all of these": if *any* overlap
exists, it exists between two **adjacent** intervals in start order, so one pass
comparing `intervals[i].start` against `intervals[i-1].end` is enough. (If `i`
and `k` overlap with `i < k`, everything between starts at or after `i`'s start
and at or before `k`'s start, which is at or before `i`'s end — so `i` overlaps
`i+1` too.)

### Sort by end → greedy keep

Walk the end-sorted list, keeping an interval whenever it starts at or after the
end of the last one you kept. Everything not kept is a removal.

**The exchange argument**, which interviewers do ask for:

> Take the interval that ends earliest. Claim: some optimal solution contains
> it. Suppose an optimal solution instead contains a different first interval
> `X`. `X` ends no earlier than the earliest-ending interval `E`, by definition
> of earliest. So swapping `X` for `E` leaves the rest of that solution's
> intervals still compatible — they all started after `X` ended, hence after `E`
> ended too. The swap doesn't reduce the count, so an optimal solution
> containing `E` exists. Recurse on the remainder.

In one line: **finishing earliest leaves the most room for everything after.**
Say it in those words. That's the [[greedy-choice]] property being asserted, and
it is why this is greedy rather than DP.

## Minimal example — the trap, side by side

**This is the trap of the topic**, and it deserves the whole example section.
After merging problems, the instinct is to sort by start again. It gives a
*wrong answer*, not a slow one, and it passes the example in the statement.

```
intervals: [1,2] [2,3] [3,4] [1,3]

sorted by END:    [1,2]  [2,3]  [1,3]  [3,4]
                  keep   keep    ✗     keep       → kept 3, removed 1  ✓

sorted by START:  [1,2]  [1,3]  [2,3]  [3,4]
                  keep    ✗?     …               ← now what? you'd have to look
                                                   ahead to know [1,3] is the
                                                   wrong one to keep
```

Under a start sort, the greedy has no local information telling it that `[1,3]`
is the greedy one that ruins things — the damage a kept interval does is
measured by *where it ends*, and a start sort hides exactly that. One long
interval swallowing several short ones is the shape that exposes it.

```python
intervals.sort(key=lambda x: x[0])   # by start → merge, conflict-check
intervals.sort(key=lambda x: x[1])   # by END   → minimum removals. The trap.
intervals.sort()                     # lexicographic: start, then end.
                                     # Fine wherever "by start" is fine.
```

## Complexity

**O(n log n) time, dominated by the sort**, for both keys. The pass afterwards is
O(n) with O(1) extra state beyond the output list. Say "dominated by the sort"
rather than reciting the number — it shows you know where the cost is.

Space: O(n) for the output; CPython's `sort` is in-place on the list but not
free in the sense you usually mean, and `sorted()` allocates a second list.

If the input is **already sorted**, don't sort — you're at O(n), and spotting
that in a constraint and not wasting it is a small thing interviewers notice.

## Gotchas

- **Sorting by start on a minimum-removals problem.** The single most expensive
  mistake in the topic. If you catch yourself typing `key=lambda x: x[0]` on a
  "remove the fewest" or "attend the most" problem, stop.
- **Assuming the reverse is symmetric.** Sorting by end does *not* work for
  merging. The two keys are not interchangeable in either direction.
- **`intervals.sort()` where you meant by end.** The bare sort is by start. It
  looks deliberate and is silently the wrong key.
- **Re-sorting an input the problem already told you is sorted.**
- **Claiming O(n) after sorting.** You sorted; it's O(n log n).
- **Sorting a list of lists and then mutating the originals.** Build output
  fresh rather than editing in place while walking.

## Related

- [[interval]] — the object and the overlap test this scan calls in its loop
- [[sort-then-scan]] — the general pattern; this page is the interval-specific
  instance where the *choice of key* carries all the correctness
- [[greedy-choice]] — what licenses the sort-by-end variant, via the exchange
  argument above
- [[sweep-line]] — the third option, for when neither key helps because the
  question is about concurrency rather than combination

## Evidence

Problems where I used this unaided:
