---
type: lesson
topic: intervals
status: ready
updated: 2026-09-14
sources: [https://neetcode.io/practice/practice/blind75, https://www.geeksforgeeks.org/merging-intervals/, https://www.geeksforgeeks.org/heap-queue-or-heapq-in-python/]
tags: [lesson, intervals]
---

# Lesson 16 — Intervals

**Curriculum:** [[../curriculum/intervals|Topic 16]] · **Skill:** [[../skills/algorithms|Algorithms]] · **Materials:** [[../meta/resources|Resources]]
**Prerequisites:** [[08-heap|Lesson 8 — Heap / Priority Queue]] (problem 5 needs it),
[[15-greedy|Lesson 15 — Greedy]] (problem 3 *is* a greedy proof).
**Work through in:** 45–60 minutes. It's a small topic with one hard idea in it.

---

## 1. Why this topic

The obvious reason: five problems, all the same shape, and the shape shows up in
real product work constantly — calendars, booking systems, rate limiters, log
windows, resource allocation. Interviewers like intervals because the problem
statement is trivially explainable in ten seconds and the solutions separate
people cleanly.

The real reason, and the one worth carrying: **this is the topic where the
pre-processing step *is* the algorithm.** Every earlier topic had a trick in the
loop — the seen-set in Topic 1, the pointer movement rule in Topic 2, the window
invariant in Topic 3. Here the loop is almost always a boring single pass. What
decides whether your answer is right is a decision you make *before* the loop
starts: **what do you sort by?**

That is a genuinely new skill. Sort by start and you can merge. Sort by end and
you can greedily pack. Sort the endpoints *separately, detached from the
intervals they came from*, and you get a sweep line that answers questions
neither of the others can. Same input, three different pre-processing choices,
three different classes of problem solved. Getting the sort key wrong doesn't
give you a slow answer — it gives you a **wrong** answer that passes the example
in the problem statement. That's the trap this topic exists to teach.

It also closes a loop back to [[15-greedy|Greedy]]: problem 3 is the cleanest
exchange-argument proof in the whole Blind 75, and you're now equipped to give
it.

---

## 2. The mental model

### An interval is a strip of masking tape on a ruler

Not a pair of numbers — a **contiguous stretch of a single axis**. The axis is
usually time, sometimes position. The two numbers are just the ends of the tape.

```
axis:  0    1    2    3    4    5    6    7    8    9   10
       ├────┼────┼────┼────┼────┼────┼────┼────┼────┼────┤

A:          ████████████                          [1, 3]
B:                    ████████████                [3, 5]
C:                              ████████████      [5, 7]
D:          ██████████████████████████            [1, 6]
```

Hold that picture. Almost every interval question is one of four physical
questions you could answer by *looking* at this strip of tape:

- **Do two pieces touch?** → the overlap test (§3)
- **Can I press overlapping pieces into one longer piece?** → merge
- **What's the fewest pieces I can peel off so none touch?** → greedy
- **At position `x`, how many layers of tape are stacked?** → sweep line

The reason people fail interval problems is that they stop seeing the tape and
start seeing `[[1,3],[2,6],[8,10]]` — four-deep nested brackets with no picture
attached. **Draw the tape. Every time. On the whiteboard, on the shared doc, on
paper next to your keyboard.** Three seconds of drawing eliminates the whole
category of case-enumeration errors below.

### Sorting is tilting the ruler

Unsorted intervals are tape thrown on the floor. Sorting by start lines every
piece up so that as you walk left to right, you never have to look backwards —
which is exactly the property that makes a single pass sufficient. That's it.
That's the whole reason we sort: **to make "everything relevant is behind me" true.**

---

## 3. The mechanics

### The overlap test — the single most valuable thing in this lesson

Two intervals `[a1, b1]` and `[a2, b2]` overlap exactly when:

```python
a1 <= b2 and a2 <= b1
```

Memorising that is worth something, but it evaporates under pressure. What
doesn't evaporate is **how to re-derive it in five seconds**, and the derivation
is the point:

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

So `disjoint = (b1 < a2) or (b2 < a1)`. Negate it — De Morgan — and you get
`overlap = (b1 >= a2) and (b2 >= a1)`, which is the same thing written the other
way round. **Two cases, one negation, done.** No diagram of five configurations,
no off-by-one.

Sanity-check it against every configuration, because a test you haven't checked
against containment is a test you don't trust:

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

Note **containment passes without a special case.** People write an explicit
`if b2 <= b1: # B is inside A` branch and then get the merge wrong. You don't
need it.

### Inclusive vs exclusive endpoints — ask the interviewer

Look at the "touching" row: `[1,3]` and `[3,5]`. Do they overlap?

- **If the interval is inclusive of both ends** (`[1,3]` covers the instant 3),
  yes. Two meetings, one ending at 3 and one starting at 3, *conflict*.
- **If the end is exclusive** (`[1,3)` covers up to but not including 3), no.
  That's how calendars actually work — a 2–3pm meeting and a 3–4pm meeting are
  fine.

The code difference is one character: `<=` vs `<`. The answer difference is the
whole problem. **This is a real clarifying question, and asking it is a
positive signal** — it says you've thought about boundaries, which is most of
what interval code is. Say out loud: *"Before I start — do intervals that touch
at an endpoint count as overlapping? I'll assume yes and use closed intervals
unless you'd rather I didn't."* Then be consistent.

For merging problems, touching usually **should** merge (`[1,3]` + `[3,5]` →
`[1,5]`), because you're describing covered ground. For meeting-room problems,
touching usually **shouldn't** count as a clash. Read the examples in the
statement — they nearly always disambiguate it for you if you look.

### Merging two overlapping intervals

If they overlap, the merged piece of tape runs from the earliest start to the
latest end:

```python
merged = [min(a1, a2), max(b1, b2)]
```

`max` on the end is the part people drop. If you've already sorted by start and
you're absorbing a new interval into the current one, `min` on the start is free
(the current one started first), but the end genuinely needs `max` — the new
interval may be **contained** in the current one, and taking its end would
*shrink* your merged interval. That's the containment bug, and it produces
wrong answers on inputs like `[[1,10],[2,3],[4,5]]`.

### The sweep line — decouple the endpoints

The third mechanic, and the one that generalises furthest. Stop thinking about
intervals as objects. Think of each one as **two events on the axis**:

```
[2, 6]   →   (2, +1)  "one more thing is now active"
             (6, -1)  "one fewer thing is active"
```

Throw all the events from all the intervals into one list, sort by position, and
walk it keeping a running counter. The counter *is* the number of intervals
covering the point you're standing on.

```
intervals: [0,4] [1,3] [2,5]

events:  (0,+1) (1,+1) (2,+1) (3,-1) (4,-1) (5,-1)
                                ↑
counter:    1      2      3      2      1      0
                          ↑
                    max = 3  ← the answer to "how many rooms?"
```

You have thrown away the information about *which* interval is which, and in
exchange you've got a problem that is now just "scan a sorted list of ±1s and
track a max." That trade is the sweep line. See
[[../concepts/sweep-line|sweep line]].

**The tie-break rule is where this goes wrong.** When a start and an end land on
the same position, which do you process first? Under exclusive-end semantics
(one meeting ends at 3, another starts at 3, no conflict) you must process the
**end first**, or your counter briefly reads one too high. Encode it in the sort
key rather than in an `if`: sort `(position, delta)` and, because `-1 < +1`,
ends sort before starts automatically at the same position. Under inclusive
semantics, flip it. Decide which you need, then make the sort key do the work.

---

## 4. Complexity

| Step | Time | Space | Why |
|---|---|---|---|
| Sort intervals (by start or by end) | **O(n log n)** | O(n) in CPython | Timsort; this dominates everything |
| Single pass after sorting | O(n) | O(1) extra | Each interval looked at once |
| Overlap test between two intervals | O(1) | O(1) | Two comparisons |
| Build + sort 2n sweep events | O(n log n) | O(n) | 2n events, still n log n |
| Sweep pass | O(n) | O(1) | One counter |
| Min-heap of end times: push/pop | O(log n) | O(n) | Heap holds ≤ n ends |
| Whole heap-based room count | O(n log n) | O(n) | n pushes + n pops |
| Insert into an *already sorted* list | **O(n)** | O(n) | No sort needed — the pre-sorted gift |

The headline: **almost every interval problem is O(n log n) time and the sort is
the reason.** When you state complexity, say *"O(n log n), dominated by the
sort"* — it shows you know where the cost is rather than reciting a number.

The one exception is the row at the bottom. If the input is **already sorted**
(and one of the five problems hands you exactly that), you're at O(n), and
sorting it again would be strictly worse. Spotting "already sorted" in a
constraint and *not* wasting it is a small thing interviewers notice.

---

## 5. The patterns

Each is **a trigger in the problem statement → the tool.** The trigger is the
transferable half.

### Pattern A — three-phase scan over a pre-sorted list
> **Trigger:** "insert a new interval into a list that is already sorted and non-overlapping"

The gift in that sentence is **already sorted and non-overlapping**. Don't
re-sort. Walk once, and recognise that the sorted list splits into exactly three
regions relative to the new interval:

```
existing:  [1,2]  [3,5]  [6,7]  [8,10]  [12,16]
new:              [4,8]
           └─┬─┘  └──────┬─────┘ └──┬──┘
          before      overlapping   after
        (end < new.start)        (start > new.end)
```

Phase 1: copy everything that ends before the new one starts, untouched.
Phase 2: while intervals overlap the new one, absorb them into it — widening
the new interval with `min` on start and `max` on end — then emit it **once**.
Phase 3: copy the rest, untouched.

Three loops, no nesting, O(n). The structural insight is that the three phases
are *disjoint and in order*, so one pass with three sequential `while`s is
enough — no flags, no re-scanning. See [[../concepts/interval|interval]].

### Pattern B — sort by start, then absorb → [[../concepts/sort-by-start|sort by start]]
> **Trigger:** "merge all overlapping intervals", "combine", "how much total ground is covered"

Sort by start. Keep the last interval in your output list as the "current" one.
For each next interval: if it overlaps the current, absorb it (`current.end =
max(current.end, next.end)`); otherwise the current one is finished, so append
the next as the new current.

```
sorted:   [1,3]  [2,6]  [8,10]  [15,18]
           ────────────
current:  [1,3] → absorb [2,6] → [1,6] → [8,10] doesn't touch → emit, new current
output:   [1,6]  [8,10]  [15,18]
```

Why sorting by start makes the single pass legal: once sorted, any interval that
could overlap the current one **must start at or after the current one's start**,
so it either overlaps the current interval or it starts strictly after the
current one ends — and if it starts after the current one ends, so does every
interval behind it. Nothing you've already emitted can ever be revisited. That
sentence is the proof, and it's worth being able to say it.

Because you sorted by start, the merged start is always just the current start —
`min` is free. The `max` on the end is not.

### Pattern C — sort by END, greedy keep → [[15-greedy|greedy]]
> **Trigger:** "minimum number to remove so none overlap", "maximum number you can attend/schedule", "non-overlapping"

**This is the trap of the topic.** The instinct after Pattern B is to sort by
start again. It gives the wrong answer.

Sort by **end**. Then walk, keeping an interval whenever it starts at or after
the end of the last one you kept. Everything you didn't keep is a removal.

```
sorted by END:  [1,2]  [2,3]  [1,3]  [3,4]
                 keep   keep    ✗     keep      → kept 3, removed 1

sorted by START: [1,2]  [1,3]  [2,3]  [3,4]
                  keep   ✗?     ...              ← now what? you'd have to
                                                   look ahead to know [1,3] is
                                                   the wrong one to keep
```

**Why end works, as an exchange argument** — this is the greedy proof and
interviewers do ask for it:

> Among all intervals, take the one that ends earliest. Claim: some optimal
> solution contains it. Suppose an optimal solution instead contains a different
> first interval `X`. `X` ends no earlier than our earliest-ending interval `E`
> (by definition of earliest). So swapping `X` for `E` leaves the rest of the
> optimal solution's intervals still compatible — they all started after `X`
> ended, hence after `E` ended too. The swap doesn't reduce the count. So an
> optimal solution containing `E` exists. Recurse on the remainder.

In one line: **finishing earliest leaves the most room for everything after.**
That's the whole intuition, and it is why this is a greedy problem and not a DP
one. Say it in those words.

### Pattern D — sort by start, check adjacent pairs only
> **Trigger:** "can a person attend all of these", "is there any conflict at all"

A yes/no existence question, not a counting one. Sort by start; if *any* overlap
exists, it exists between two **adjacent** intervals in the sorted order. So one
pass comparing `intervals[i].start` against `intervals[i-1].end` settles it — no
nested loop over all pairs.

That "adjacent is sufficient" claim is the whole insight and it deserves a
sentence of justification: if `i` and `k` overlap with `i < k` in sorted order,
then everything between them starts at or after `i`'s start and at or before
`k`'s start, which is at or before `i`'s end — so `i` overlaps `i+1` too. You
never need to look further than one step back.

Here in particular, **ask about touching endpoints before you write `<` or
`<=`.** A meeting ending at 3 and one starting at 3 is the entire question.

### Pattern E — the sweep / min-heap of end times → [[../concepts/sweep-line|sweep line]]
> **Trigger:** "minimum number of rooms/resources/machines", "maximum concurrent", "how many at once", "peak"

The word to listen for is **concurrent**. The answer is the maximum stack depth
of the tape, and there are two equivalent ways to get it.

**Sweep (usually cleaner):** split every interval into `(start, +1)` and
`(end, -1)`, sort all 2n events, run a counter, track its maximum. §3 has the
picture. Handles the tie-break in the sort key.

**Min-heap of end times (usually more explainable):** sort by start. Walk. Keep
a min-heap of the end times of currently-running intervals. At each new
interval, pop every end time that is `<=` its start — those rooms freed up — then
push its own end. The heap's size at any moment is the number of rooms in use;
its maximum is the answer. This leans on [[08-heap|Topic 8]]: `heapq` gives you
"which of the in-flight things finishes soonest" in O(log n), and *that* is the
only question you ever ask of the set of active intervals.

Both are O(n log n). The heap version tells a better story out loud ("this heap
is the set of rooms currently occupied, ordered by who frees up first"); the
sweep version is fewer lines and generalises to weighted events. Know both, lead
with whichever you can narrate.

---

## 6. Python notes

```python
intervals.sort(key=lambda x: x[0])      # by start   → Patterns B, D, E
intervals.sort(key=lambda x: x[1])      # by END     → Pattern C. The trap.
intervals.sort()                        # tuples/lists sort lexicographically:
                                        # by start, then by end. Fine for B/D.

import heapq
heap = []
heapq.heappush(heap, end_time)          # O(log n)
heap[0]                                 # peek the SMALLEST — O(1), no pop
heapq.heappop(heap)                     # O(log n)
len(heap)                               # rooms currently in use
```

`heapq` is a **min-heap only**. That's exactly what you want here — you always
care about the *earliest* end time. (For a max-heap you push negated values;
you won't need it in this topic.)

Two idioms that keep interval code readable:

```python
for start, end in intervals:            # unpack in the loop header, always.
    ...                                 # `iv[0]`/`iv[1]` everywhere reads badly
                                        # and is where off-by-ones hide.

output[-1][1] = max(output[-1][1], end) # mutate the last emitted interval in
                                        # place — the standard merge move
```

Building sweep events:

```python
events = []
for start, end in intervals:
    events.append((start, 1))
    events.append((end, -1))
events.sort()      # (position, delta); -1 sorts before +1 at equal position,
                   # so ends are processed first. Exclusive-end semantics.
```

Note that `events.sort()` with no key gets the tie-break right *for free* under
exclusive-end semantics — but only by accident of `-1 < 1`. Say why it works out
loud; don't let the interviewer think you got lucky.

---

## 7. Traps

- **Sorting by start on the "remove the fewest" problem.** The single most
  expensive mistake in this topic. It produces a plausible answer that's wrong on
  inputs with one long interval swallowing several short ones. Pattern C is
  **sort by end**. If you catch yourself writing `key=lambda x: x[0]` on a
  minimum-removals problem, stop.
- **Taking the new end instead of `max(old_end, new_end)` when merging.** Breaks
  on containment: `[[1,10],[2,3]]` merges to `[1,3]` instead of `[1,10]`. Test
  containment explicitly, every time.
- **Enumerating overlap cases and missing one.** Use the complement: they're
  disjoint iff one ends before the other starts. Two cases. Negate.
- **Never asking about inclusive vs exclusive endpoints.** `<` vs `<=` changes
  the answer. Asking is a positive signal; guessing silently and getting it
  backwards looks careless.
- **Re-sorting an input the problem already told you is sorted.** You turned an
  O(n) solution into O(n log n) and threw away the one hint you were given.
- **Sweep-line tie-breaks left to chance.** Starts and ends at the same position
  must be ordered deliberately. Put it in the sort key, not in a branch.
- **Mutating the input list while iterating it.** Build a new output list.
  Removing from a list you're walking is a reliable way to skip elements.
- **Empty input.** `[]` should return `[]` or `0`, not crash on
  `intervals[0]` or `output[-1]`. A single interval is the other freebie case.
  Check both before you say "done".
- **Forgetting the sort cost in your stated complexity.** "O(n)" is wrong if you
  sorted. Say "O(n log n), dominated by the sort."

---

## 8. Worked example

Outside the problem set, so nothing is spoiled. This is the sweep line doing
something Patterns B–D can't.

> **Problem (Car Pooling).** You drive a car with `capacity` seats. You're given
> trips as `[num_passengers, from, to]`. Passengers get on at `from` and off at
> `to`. Return whether you can make every trip without ever exceeding capacity.
>
> `trips = [[2,1,5],[3,3,7]]`, `capacity = 4` → `False`
> (between 3 and 5 you're carrying 5 people).

**Brute force.** For each kilometre marker on the route, loop every trip and sum
the passengers on board there. O(range × n). Say this out loud first — it names
the quantity you actually care about (*occupancy at a point*) before you optimise.

**Spot the pattern.** "Never exceeds capacity" is a question about the **maximum
concurrent load**, and the load only changes at a pickup or a dropoff. That's
**Pattern E**. The twist versus a room-count problem is that events carry a
weight (`num_passengers`) rather than a plain ±1 — which the sweep absorbs
without any change of shape, and the heap version doesn't handle nearly as
neatly. That's the reason to know both.

```python
def car_pooling(trips, capacity):
    events = []
    for num, start, end in trips:
        events.append((start, num))      # +num board here
        events.append((end, -num))       # -num alight here

    events.sort()                        # by position; at equal position,
                                         # negative deltas sort first, so people
                                         # get off before the next group gets on
    onboard = 0
    for _, delta in events:
        onboard += delta
        if onboard > capacity:
            return False
    return True
```

**Complexity.** O(n log n) time, dominated by sorting 2n events. O(n) space for
the events list.

**The move to internalise:** the moment the question is *"how many are active at
once"*, stop treating intervals as objects. Cut each one into a start event and
an end event, throw away which interval they came from, sort, and run a counter.
Every "peak load", "maximum concurrent", "minimum resources" question is that
same three-step move — and the tie-break at equal positions is where the
correctness actually lives, so decide it deliberately.

---

## 9. Materials

Full list on [[../meta/resources|Resources]]. For this lesson, in order:

1. [Merging intervals](https://www.geeksforgeeks.org/merging-intervals/) — 10 min.
   Pattern B with the proof spelled out.
2. [Activity selection problem](https://www.geeksforgeeks.org/activity-selection-problem-greedy-algo-1/)
   — 15 min. This is Pattern C under its classical name, with the exchange
   argument written properly. **Read this one carefully** — it's the theory
   behind the topic's trap.
3. [heapq in Python](https://www.geeksforgeeks.org/heap-queue-or-heapq-in-python/)
   — skim if Topic 8 is fresh, read if it isn't. You need `heappush`,
   `heappop`, `heap[0]`, `len`.
4. [Python `heapq` docs](https://docs.python.org/3/library/heapq.html) — the
   reference. Note it's min-only.
5. Search GeeksforGeeks for "sweep line algorithm" — the CS-theory framing,
   worth 10 minutes for the vocabulary if nothing else.
6. [NeetCode on YouTube](https://www.youtube.com/@NeetCode) — one video per
   problem, but **only after** a timed attempt and a debrief.

---

## 10. Self-check

Answer these **aloud**, in full sentences, before starting the timer. Any that
come out mumbled, go back to §3.

1. Two intervals `[a1,b1]` and `[a2,b2]`. State the overlap test — and then
   *derive* it from the two disjoint cases rather than reciting it.
2. Why does the overlap test handle full containment with no special case?
3. `[1,3]` and `[3,5]` — do they overlap? What question do you ask the
   interviewer, and why does it matter?
4. When you sort by start, what property becomes true that makes a single pass
   sufficient? Say the proof sentence.
5. Merging a sorted run: why is `min` on the start free but `max` on the end
   mandatory? Give the input that breaks it.
6. "Remove the fewest intervals so none overlap." What do you sort by, and what
   is the exchange argument that proves it optimal?
7. Describe the sweep line in three steps, with the tie-break rule.
8. For "minimum meeting rooms", give both the heap solution and the sweep
   solution, and say which you'd narrate and why.
9. What's the time complexity of essentially every problem in this topic, and
   which step is responsible?
10. Give the trigger phrase for each of Patterns A–E.

---

## 11. Ready?

**First timed problem: Insert Interval** (Medium) — Pattern A.

Say `timed insert-interval` and I'll give you the statement and start the
30-minute box. No hints while it's running.

**What "good" looks like in the box:**

- You **draw the tape** before writing code. Sketch the existing intervals on an
  axis and the new one over the top. If there's no drawing anywhere, you're
  doing it the hard way.
- You spot and *say* that the input is already sorted and non-overlapping, and
  that you therefore will not sort — and that this makes the solution O(n), not
  O(n log n).
- You ask the endpoint question (touching → merge or not?) before coding.
- You write the overlap condition once, correctly, from the complement
  derivation — not by staring at five diagrams.
- You handle: empty input list, a new interval before everything, after
  everything, containing everything, and contained inside one existing interval.
  Five cases, all cheap to check, all classic. Name them before you're asked.
- You state O(n) time and O(n) space (the output list) unprompted.

Then take **Merge Intervals** next — it's Pattern B and it's the mental model in
its purest form. **Non-overlapping Intervals is third and it is the one to slow
down on**; if you find yourself typing `key=lambda x: x[0]` there, stop and
re-read Pattern C.

Not finishing a Medium is expected. It goes in the
[[../meta/review-queue|review queue]] at +2 days and comes back around. **The
plan is to repeat until it's cold-solvable, not to clear the list once.**
