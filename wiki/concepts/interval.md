---
type: concept
status: unseen
updated: 2026-09-14
sources: [https://www.geeksforgeeks.org/merging-intervals/]
tags: [intervals]
---

# Interval

**In one sentence, in my own words** — a `[start, end]` pair treated as a single
object: a contiguous strip of one axis, usually time, where the two numbers are
just the ends of the tape and every question you'll be asked is a physical
question about the tape.

The core object of [[../lessons/16-intervals|Lesson 16 — Intervals]].

## When to reach for it

You don't reach for an interval; the problem hands you one. What you reach for
is **the picture**. The trigger is any input shaped `[[a, b], [c, d], ...]`, or
any statement about meetings, bookings, ranges, windows of time, segments of a
road.

The failure mode this page exists to prevent: people stop seeing tape and start
seeing `[[1,3],[2,6],[8,10]]` — four-deep nested brackets with no picture
attached — and then try to reason about overlap by enumerating cases in their
head.

**Draw the tape. Every time.** Three seconds of drawing eliminates the entire
category of case-enumeration errors.

```
axis:  0    1    2    3    4    5    6    7    8    9   10
       ├────┼────┼────┼────┼────┼────┼────┼────┼────┼────┤

A:          ████████████                          [1, 3]
B:                    ████████████                [3, 5]
C:                              ████████████      [5, 7]
D:          ██████████████████████████            [1, 6]
```

Almost every interval question is one of four things you could settle by
*looking* at that strip:

| Question about the tape | Tool |
|---|---|
| Do two pieces touch? | the overlap test, below |
| Can I press overlapping pieces into one? | merge, [[sort-by-start]] |
| Fewest pieces to peel off so none touch? | greedy, sort by **end** |
| How many layers are stacked at position `x`? | [[sweep-line]] |

## How it works

### The overlap test, derived rather than memorised

Two intervals `[a1, b1]` and `[a2, b2]` overlap exactly when:

```python
a1 <= b2 and a2 <= b1
```

Memorising that is worth something, but it evaporates under pressure. What
doesn't evaporate is **how to re-derive it in five seconds**:

> Don't enumerate the ways they *can* overlap — there are five and you will miss
> one. Enumerate the ways they **can't**. There are exactly two, and they're
> symmetric.

```
NOT overlapping, case 1:  A entirely before B     →  b1 < a2
       A: ████
       B:          ████

NOT overlapping, case 2:  B entirely before A     →  b2 < a1
       A:          ████
       B: ████
```

So `disjoint = (b1 < a2) or (b2 < a1)`. Negate it — De Morgan flips the `or` to
an `and` and each `<` to `>=`:

```
overlap = (b1 >= a2) and (b2 >= a1)
        = (a2 <= b1) and (a1 <= b2)        ← same thing, read right to left
```

**Two cases, one negation, done.** That is the whole derivation and it is the
single most valuable thing in the topic.

### Check it against every configuration

A test you haven't checked against containment is a test you don't trust:

```
identical      A: ████        a1<=b2 ✓  a2<=b1 ✓   → overlap ✓
               B: ████

partial        A: ████        a1<=b2 ✓  a2<=b1 ✓   → overlap ✓
               B:   ████

contained      A: ████████    a1<=b2 ✓  a2<=b1 ✓   → overlap ✓
               B:   ████

touching       A: ████        a1<=b2 ✓  a2<=b1 ✓   → overlap ✓  (see below)
               B:     ████

disjoint       A: ████        a1<=b2 ✓  a2<=b1 ✗   → no        ✓
               B:       ████
```

Note that **containment passes with no special case.** People write an explicit
`if b2 <= b1: # B is inside A` branch, and then get the merge wrong inside it.
The branch isn't needed. One expression covers all five rows.

### Merging two that overlap

```python
merged = [min(a1, a2), max(b1, b2)]
```

Earliest start, latest end. The `max` on the end is the half people drop: if
you've already sorted by start and you're absorbing a new interval into the
current one, `min` on the start is free — the current one started first — but
the end genuinely needs `max`, because the new interval may be **contained** in
the current one and taking its end would *shrink* what you'd already covered.
`[[1,10],[2,3]]` merging to `[1,3]` is that bug.

### Inclusive vs exclusive endpoints — the clarifying question

Look at the touching row: `[1,3]` and `[3,5]`. Do they overlap?

- **Closed / inclusive** (`[1,3]` covers the instant 3): **yes**. Two meetings,
  one ending at 3 and one starting at 3, conflict.
- **Half-open / exclusive end** (`[1,3)` covers up to but not including 3):
  **no**. That's how calendars actually work — a 2–3pm and a 3–4pm meeting are
  fine.

The code difference is one character, `<=` versus `<`. The answer difference is
the whole problem.

**Ask.** It's a real clarifying question and asking it is a positive signal — it
says you've thought about boundaries, which is most of what interval code is.
The line, roughly:

> *"Before I start — do intervals that touch at an endpoint count as
> overlapping? I'll assume yes and use closed intervals unless you'd rather I
> didn't."*

Then be consistent for the rest of the problem. Defaults that usually apply:

| Problem shape | Touching should… |
|---|---|
| Merging / "total ground covered" | **merge** — `[1,3]` + `[3,5]` → `[1,5]` |
| Meeting rooms / "can they attend all" | **not** count as a clash |

And read the examples in the statement — they nearly always disambiguate it for
you if you look.

## Complexity

The object itself is free: an interval is two numbers, O(1) space, and the
overlap test is two comparisons, O(1) time.

What costs is arranging them. Essentially every interval problem is
**O(n log n) time, dominated by the sort** — say it in those words rather than
reciting a number. The exception is an input the problem already tells you is
sorted, which is O(n), and re-sorting it throws away the one hint you were
given.

## Gotchas

- **Enumerating overlap cases and missing one.** Use the complement. Two cases,
  negate.
- **A special-case branch for containment.** Unnecessary, and it's where the
  merge bug gets introduced.
- **Taking the new end rather than `max(old_end, new_end)`.** Breaks on
  containment. Test `[[1,10],[2,3]]` explicitly, every time.
- **Never asking about inclusive vs exclusive.** Guessing silently and getting
  it backwards looks careless; asking costs five seconds.
- **Mixing semantics mid-solution** — `<=` in the overlap test and `<` in the
  sweep tie-break. Pick one and hold it.
- **Indexing `iv[0]`/`iv[1]` everywhere.** Unpack in the loop header —
  `for start, end in intervals:` — because that's where off-by-ones hide.
- **Empty input.** `[]` should return `[]` or `0`, not crash on `intervals[0]`
  or `output[-1]`. A single interval is the other freebie case.
- **Mutating the input list while iterating it.** Build a new output list.

## Related

- [[sort-by-start]] — what to do with a *set* of intervals; the sort key is the
  decision, where this page is about a single pair
- [[sweep-line]] — the move that stops treating an interval as an object at all
  and cuts it into two independent events
- [[sort-then-scan]] — the general shape both of those are instances of
- [[greedy-choice]] — what licenses the "sort by end and keep" family; the
  interval exchange argument is the cleanest one in the Blind 75
- [[heap]] — the alternative to a sweep for concurrency questions, holding the
  end times of the currently-active intervals

## Evidence

Problems where I used this unaided:
