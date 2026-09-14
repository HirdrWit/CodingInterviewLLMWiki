---
type: lesson
topic: sliding-window
status: ready
updated: 2026-09-14
sources: [https://www.geeksforgeeks.org/window-sliding-technique/, https://www.geeksforgeeks.org/complexity-cheat-sheet-for-python-operations/]
tags: [lesson, sliding-window]
---

# Lesson 3 — Sliding Window

**Curriculum:** [[../curriculum/sliding-window|Topic 3]] · **Materials:** [[../meta/resources|Resources]]
**Builds on:** [[../lessons/01-arrays-hashing|Lesson 1]] (frequency maps, seen-sets) and
[[../curriculum/two-pointers|Topic 2]] (two pointers)
**Work through in:** 60–90 minutes. Four problems, and the last one is a Hard —
the only Hard you'll meet this early in the list. That's not an accident; see §1.

---

## 1. Why this topic

The obvious reason: "find the best contiguous stretch of this array/string" is
an enormous problem family, and the naive answer — try every stretch — is O(n²)
or worse. Sliding window collapses that to O(n).

The real reason is a change in what you're allowed to think about.

Topic 1 taught you to store what you've seen. Topic 2 taught you to move two
indices under a rule. **Sliding window is where those two combine into something
neither one was: a window that carries *state*.** Up to now, a pointer was just
a position. From here on, the pair of pointers has a meaning — everything
between them — and you maintain a summary of that meaning incrementally as the
pointers move.

That incremental-maintenance instinct is the transferable thing. It's the same
instinct behind prefix sums, behind monotonic stacks, behind most of the
"recompute nothing" optimizations you'll meet later. Sliding window is where
it's cheapest to learn, because the window is a physical object you can draw.

It also matters that this topic ends on **Minimum Window Substring**, a Hard.
Four problems here span the full range from "one-pass scan with a running
minimum" to "two counting structures kept in sync under a two-sided invariant".
If you can build that last one cold, you have the pattern for good.

---

## 2. The mental model

### A window is a frame you drag across a strip of film

Not two independent pointers — **one object with two edges.** The right edge
pulls new frames in; the left edge lets old frames fall out. The thing you care
about is what's currently inside the frame.

```
nums:   [ 2 ,  1 ,  5 ,  1 ,  3 ,  2 ]
              ╔═══════════════╗
              ║  1    5    1  ║              window = nums[1..3], sum = 7
              ╚═══════════════╝
              ^               ^
             left           right
```

Slide the right edge one step: **one element enters.** Slide the left edge one
step: **one element leaves.** Nothing else changes. That's the entire lever.

### The lever: entering and leaving are O(1)

Here is the naive way to answer "what is the largest sum of 3 consecutive
elements":

```
[2,1,5]  → 8        each box re-added from scratch
  [1,5,1]  → 7      3 additions, every time
    [5,1,3]  → 9
      [1,3,2]  → 6
```

Every step recomputes the whole window: O(n × k). But look at what actually
changed between box one and box two — `2` left, `1` entered. Everything else was
recounted for no reason.

```
sum = 8
  slide:  −nums[0]=2   +nums[3]=1   →  sum = 7      two operations, not three
  slide:  −nums[1]=1   +nums[4]=3   →  sum = 9
  slide:  −nums[2]=5   +nums[5]=2   →  sum = 6
```

**The window's state is updated by the delta, never rebuilt.** That is the whole
technique. Everything else in this lesson is about *what* the state is (a sum, a
set, a frequency map) and *when* the left edge is allowed to move.

### The lineage

[[../concepts/two-pointers|Two pointers]] and sliding window are the same
machinery pointed in different directions:

| | Two pointers | Sliding window |
|---|---|---|
| Pointers move | toward each other, from the ends | both forward, left trailing right |
| You care about | the **pair** at the two ends | the **range** between them |
| Input usually | sorted | order matters, sorting would destroy the problem |
| Trigger word | "pair", "triple", "sum to target" | "substring", "subarray", "contiguous", "window" |

If a problem says *contiguous* and sorting it would be nonsense, you are in
sliding-window territory, not two-pointer territory.

---

## 3. The mechanics — the grow/shrink decision

This is the part people get wrong, so it gets its own section. The skeleton
never changes:

```python
left = 0
for right in range(len(s)):
    # 1. ADMIT: s[right] enters the window; update the state
    while <the window should not stay this way>:
        # 2. EVICT: s[left] leaves the window; undo its effect on the state
        left += 1
    # 3. RECORD: the window is now in the shape we want — take the answer from it
```

Three slots. The problem only ever changes what goes in them. The hard slot is
the `while` condition, and there are exactly **two shapes** it takes:

### Shape 1 — maximizing: *shrink while the window is invalid*

> "Find the **longest** stretch that satisfies P."

Grow greedily. The moment admitting `s[right]` breaks P, evict from the left
until P holds again. Record the length **after** the while loop — at that point
the window is guaranteed valid, and it's the longest valid window ending at
`right`.

```
P = "no repeated characters"

a b c a b b
╚═════╝            "abc" valid, len 3
╚═══════╝          admit 'a' → "abca" INVALID, shrink
  ╚═════╝          "bca" valid again, len 3
```

### Shape 2 — minimizing: *shrink while the window is still valid*

> "Find the **shortest** stretch that satisfies P."

Grow until P first holds. Then squeeze from the left as hard as you can while P
*still* holds, recording the length on every squeeze. The answer is recorded
**inside** the while loop, because the last valid window before it breaks is the
tightest one.

```
P = "contains everything we need"

... ╚══════════╝     valid, len 6, record
... ╔═╝                shrink → still valid, len 5, record
...   ╔═╝              shrink → still valid, len 4, record
...     ╔═╝            shrink → INVALID, stop; best = 4
```

**Memorise the fork, not the code:** *longest* → record after shrinking;
*shortest* → record while shrinking. Getting this backwards is the single most
common way a sliding-window solution comes out subtly wrong, and it is the first
thing I'll check at debrief.

### Why it's O(n) and not O(n²)

There's a nested loop in there. It still isn't quadratic, and interviewers will
ask you to justify that.

`right` advances exactly n times. `left` only ever advances, never resets, and
can never pass `right` — so across the *entire run* it advances at most n times
too. Total pointer movement ≤ 2n. The inner `while` doesn't multiply the outer
loop; it **shares a budget with it.** This is an *amortized* argument, the same
kind that makes `list.append` O(1), and saying the word "amortized" out loud
here is worth real credit.

The trap on the other side: if the work inside the loop isn't O(1), the argument
collapses. Rebuilding a set, calling `max()` over a frequency map, or slicing
the string inside the loop each drag an O(k) factor back in. **The window state
must be updatable by delta.** See §7.

---

## 4. Complexity

| Approach | Time | Space | Why |
|---|---|---|---|
| Brute force: every subarray, recompute | O(n³) | O(1) | n² subarrays × O(n) to evaluate each |
| Brute force: every subarray, running total | O(n²) | O(1) | n² subarrays, O(1) to extend each |
| Fixed window, size k | **O(n)** | O(1) | one add + one remove per step |
| Variable window, O(1) state update | **O(n)** | O(1) or O(k) | each pointer moves ≤ n times total |
| Variable window, O(k) state update | O(n·k) | O(k) | the anti-pattern — see §7 |

The space term is whatever the state costs: nothing for a running sum, O(k) for
a [[../concepts/frequency-map|frequency map]] or a
[[../concepts/seen-set|seen-set]] over the window. For lowercase-ASCII problems
the alphabet is bounded at 26, so that map is **O(1) space, not O(n)** — say so.
That's [[../concepts/constraint-bounded-complexity|constraint-bounded
complexity]], the same read-the-constraints move from Lesson 1.

---

## 5. The patterns

One per problem shape in this topic. Trigger first — that's the half that
transfers.

### Pattern A — running extreme, one pass → [[../concepts/sliding-window|sliding window]]
> **Trigger:** "best pair where one comes *before* the other", "max profit", "biggest drop/rise"

The degenerate window: the left edge doesn't really slide, it **jumps** to a new
best-so-far. Scan once carrying the best value seen *before* the current index,
and at each position ask what the answer would be if this position were the end.

```
prices:  7   1   5   3   6   4
min so far:
         7   1   1   1   1   1     ← only ever decreases
answer at i = price[i] − min_so_far
         -   0   4   2   5   3     ← take the max of this row
```

The insight to name out loud: **you never need to consider two candidates
simultaneously.** Fixing the right end turns a pair-search into a lookup of one
remembered number. Order matters and you cannot sort — that's what rules out
[[../concepts/two-pointers|two pointers]] here.

### Pattern B — variable window + seen-set → [[../concepts/variable-window|variable window]]
> **Trigger:** "longest substring **without repeating** …", "all distinct", "no duplicates in the range"

Shape 1 from §3. State is a [[../concepts/seen-set|set]] of the window's
contents. Admit `s[right]`; while it's already present, evict from the left
until it isn't. The set makes "does the window already contain this?" O(1) —
exactly the Lesson 1 trade, now applied to a moving range rather than the whole
input.

The refinement worth knowing *after* you've done it the plain way: store
`char → last index` in a map instead of a set, and the left edge can jump
straight past the duplicate in one step instead of evicting one at a time. Same
O(n), fewer operations, and a good thing to offer as "here's how I'd tighten it".

### Pattern C — window + frequency map + an invariant → [[../concepts/window-invariant|window invariant]]
> **Trigger:** "longest … if you may change/replace/delete at most **k**", "at most k of something"

The `k` budget in the statement is the tell. Keep a
[[../concepts/frequency-map|frequency map]] of the window and define the cost of
the current window in terms of it — typically `window_length − (count of the most
common element)` = how many positions would have to change. The invariant is
`cost ≤ k`; shrink while it's violated. Shape 1 again.

The subtle part, and the thing that makes this a genuine Medium: how you track
"most common". Recomputing `max(counts.values())` inside the loop is O(26) or
O(k) per step, which technically still passes but breaks the clean O(n) story.
There's a well-known argument that a **never-decreasing** running max is enough
here, because the answer can only ever be improved by a larger max. Work out
*why* that's safe yourself — it's a favourite follow-up, and reciting it without
understanding it shows immediately.

### Pattern D — two-sided counting: need vs have → [[../concepts/window-invariant|window invariant]]
> **Trigger:** "**smallest** window containing all of …", "minimum substring covering …"

Shape 2 from §3, and the hardest bookkeeping in the topic. You need two
structures: what the target **requires**, and what the window currently
**holds**. Then one integer — how many distinct requirements are currently
satisfied — so that "is the window valid?" is an O(1) comparison instead of a
map-versus-map scan every step.

That collapse of "compare two dictionaries" down to "compare two integers" is
the actual idea being tested. Everything else is careful incrementing. Expect to
not finish this one in 30 minutes on the first attempt; that is the normal
outcome and it goes straight back in the [[../meta/review-queue|queue]].

---

## 6. Python notes

```python
from collections import Counter, defaultdict

counts = defaultdict(int)     # no KeyError on counts[c] += 1
counts[c] += 1                # admit
counts[c] -= 1                # evict — NOT del; see below
if counts[c] == 0:
    del counts[c]             # only if you need len(counts) to mean "distinct in window"

need = Counter(target)        # the requirements, built once, never mutated
window_len = right - left + 1 # inclusive on both ends. Write this down and check it.
```

Three that bite:

```python
s[left:right+1]          # O(k) — a COPY. Never inside the loop.
max(counts.values())     # O(distinct) — fine at 26, not free. Know you paid it.
counts.pop(c, None)      # safe delete; bare del raises KeyError
```

And the identity that decides every off-by-one in this topic:

```
window nums[left..right] inclusive  →  length = right - left + 1
```

Half the bugs in a sliding-window solution are that `+ 1`. Write the formula in
a comment before you write the loop.

---

## 7. Traps

- **Recomputing the window instead of updating it.** Rebuilding a set, slicing
  the string, or re-summing inside the loop turns O(n) into O(n·k) while *looking*
  like a sliding window. If you can't update the state in O(1) on admit and on
  evict, it isn't one yet.
- **Recording the answer in the wrong place.** Longest → after the shrink loop.
  Shortest → inside it. Getting this backwards produces a solution that passes
  the examples and fails on the hidden tests.
- **Evicting without undoing the state.** `left += 1` on its own is a bug. The
  element leaving must be removed from the sum / set / counter in the same
  breath. Keep admit and evict visually symmetric so a missing undo is obvious.
- **`while` written as `if`.** One eviction is rarely enough — after admitting a
  character you may need to evict several. `if` silently leaves the window
  invalid.
- **Letting `left` overtake `right`.** Guard the condition, and be sure the
  window can legally be empty in your formulation.
- **Claiming O(n) without justifying the nested loop.** Say the amortized
  argument: each pointer advances at most n times in total. Unprompted.
- **Missing the bounded alphabet.** "O(1) space, since the map holds at most 26
  keys" is a better answer than "O(n) space" and shows you read the constraints.
- **Negative numbers.** The "shrink while the sum is too big" logic assumes
  growing the window can only *increase* the sum. With negatives that monotonicity
  dies and sliding window is the wrong tool — a prefix-sum + hash map is the
  usual replacement. Check the constraints for non-negativity before you commit.
- **Empty and degenerate inputs.** `""`, a single character, `k = 0`, a target
  longer than the source. Check them before saying "done".

---

## 8. Worked example

Not from the problem set, so nothing is spoiled. This one is the cleanest
possible instance of Shape 2.

> **Problem.** Given an array of **positive** integers `nums` and a positive
> integer `target`, return the length of the **shortest contiguous subarray**
> whose sum is ≥ `target`. Return `0` if there is none.
> `nums = [2,3,1,2,4,3]`, `target = 7` → `2`, from `[4,3]`.

**Brute force.** Every start, extend to every end, track the sum: O(n²). Say
this first, always — it proves you understood the problem and gives you a
baseline to beat.

**Spot the pattern.** "Contiguous" + "shortest" + "satisfying a condition" →
sliding window, Shape 2. And the condition is monotonic in the right direction:
all values are positive, so growing the window can only raise the sum and
shrinking it can only lower the sum. That monotonicity is what makes the greedy
squeeze safe — call it out, because it's the load-bearing assumption.

```python
def min_subarray_len(target, nums):
    left = 0
    total = 0
    best = float('inf')

    for right, value in enumerate(nums):
        total += value                      # ADMIT  — O(1)

        while total >= target:              # still valid → squeeze
            best = min(best, right - left + 1)   # RECORD inside: Shape 2
            total -= nums[left]             # EVICT  — undo, symmetric to admit
            left += 1

    return 0 if best == float('inf') else best
```

Trace it on `[2,3,1,2,4,3]`, `target = 7`:

```
right=0  [2]              total=2
right=1  [2,3]            total=5
right=2  [2,3,1]          total=6
right=3  [2,3,1,2]        total=8  ≥7 → record 4, evict 2 → total=6
right=4  [3,1,2,4]        total=10 ≥7 → record 4, evict 3 → total=7
                                   ≥7 → record 3, evict 1 → total=6
right=5  [2,4,3]          total=9  ≥7 → record 3, evict 2 → total=7
                                   ≥7 → record 2, evict 4 → total=3   ← best
```

**Complexity.** O(n) time — `right` moves n times, `left` moves at most n times
across the whole run, and admit/evict are each O(1). O(1) space.

**The moves to internalise:**

1. The `while` is the whole algorithm. Deciding *shrink while valid* versus
   *shrink while invalid* is the design decision; the rest is arithmetic.
2. Admit and evict are mirror images. Write them as a pair or you will forget
   one.
3. Name the monotonicity you're relying on. Here: all values positive. If the
   interviewer says "now allow negatives", the right answer is "then this
   technique no longer applies, and here's why" — not a patch.

---

## 9. Materials

Full list on [[../meta/resources|Resources]]. For this lesson, in order:

1. [Window sliding technique](https://www.geeksforgeeks.org/window-sliding-technique/)
   — 10 min. Fixed-size windows; the delta-update idea at its simplest.
2. Search GeeksforGeeks for *"smallest subarray with sum greater than a given
   value"* — the article version of §8, worth reading after you've coded it.
3. [Python complexity cheat sheet](https://www.geeksforgeeks.org/complexity-cheat-sheet-for-python-operations/)
   — re-skim the slicing and `max()` rows specifically. They're the ones that
   quietly break the O(n) claim here.
4. [NeetCode on YouTube](https://www.youtube.com/@NeetCode) — search the problem
   name, but **only after** a timed attempt and a debrief.
5. Optional, if Pattern C or D won't stick:
   [LeetCode 75](https://leetcode.com/studyplan/leetcode-75/) has a sliding
   window section for extra reps on the same shapes with different problems.

---

## 10. Self-check

Aloud, in full sentences, before starting the timer. Any that come out mumbled,
go back to §3.

1. What makes sliding window different from two pointers — in terms of what you
   care about, not how the indices move?
2. What is the one operation that makes the technique O(n) instead of O(n·k)?
3. Write the three-slot skeleton from memory. Name each slot.
4. For a **longest** problem, where does the answer get recorded? For a
   **shortest**? Why the difference?
5. Justify O(n) despite the nested loop, in one sentence, using the word
   "amortized".
6. Give a concrete thing you could do inside the loop that would silently make
   the solution O(n²).
7. Why does sliding window break on arrays containing negative numbers, for
   sum-based conditions?
8. Length of the inclusive window `nums[left..right]`? No hesitating.
9. Give the trigger phrase for each of patterns A–D.

---

## 11. Ready?

**First timed problem: Best Time to Buy and Sell Stock** (Easy) — Pattern A.

Say `timed best-time-to-buy-and-sell-stock` and I'll give you the statement and
start the 30-minute box. No hints while it's running.

**What "good" looks like here** — it's an Easy and it's short, so finishing is
not the bar:

- You state the O(n²) brute force and its complexity *before* writing the fast
  version
- You articulate **why order matters** — that the buy must precede the sell is
  exactly what forbids sorting
- One pass. If you're storing every price to look back at later, you've missed it
- You state time **and** space unprompted
- You test: strictly decreasing prices (answer is 0, not negative), a single
  price, an empty list
- Clean names — `min_price`, `best_profit`, not `m` and `b`

If you finish well inside 30 minutes, don't stop the clock — roll straight into
**Longest Substring Without Repeating Characters** and spend what's left on it.

Then the topic gets harder fast. **Minimum Window Substring** is a Hard and is
very unlikely to fall in one box. That is expected and is not a failure — it
goes in the [[../meta/review-queue|review queue]] at +2 days and comes back
until it's cold-solvable. **Repeat until confident is the plan, not the
fallback.**
