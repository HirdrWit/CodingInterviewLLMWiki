---
type: lesson
topic: two-pointers
status: ready
updated: 2026-09-14
sources: [https://www.geeksforgeeks.org/two-pointers-technique/, https://www.geeksforgeeks.org/sorting-algorithms/, https://www.geeksforgeeks.org/complexity-cheat-sheet-for-python-operations/]
tags: [lesson, two-pointers]
---

# Lesson 2 — Two Pointers

**Curriculum:** [[../curriculum/two-pointers|Topic 2]] · **Materials:** [[../meta/resources|Resources]]
**Prerequisites:** [[01-arrays-hashing|Lesson 1]] — specifically pattern C, the
complement lookup, and the sorted-vs-unsorted fork it flagged.
**Work through in:** 45–60 minutes. Shorter than Lesson 1, and only three
problems — but two of them are Mediums, and one of them (Three Sum) is among the
most-asked questions in the entire set.

---

## 1. Why this topic now

The obvious reason: it's the next box on the NeetCode list.

The real reason: **Lesson 1 taught you to buy time with memory. This lesson
teaches you to buy time with *order* — and get the memory back.**

Lesson 1 section 5 pattern C ended on a fork that was left deliberately hanging:

> Sorted → two pointers. Unsorted → hash map. That fork comes back constantly.

This is where that fork gets paid off. Two Sum, solved with a hash map, is O(n)
time and **O(n) space**. The same problem on a *sorted* array is O(n) time and
**O(1) space** — no auxiliary structure at all, just two indices walking toward
each other. The sortedness did the work the hash map was doing.

That's the transferable idea, and it's bigger than this topic: **structure in
the input is a resource.** A sorted array isn't just an array you happen to have
sorted — it carries an invariant ("everything left of me is smaller") that lets
you *rule out* whole regions of the search space without looking at them. An
interviewer who says "the array is sorted" has handed you something. Failing to
spend it is a wrong answer even if your code returns the right values.

The follow-up "can you do that in O(1) space?" is one of the most common
escalations in a real screen. This topic is the answer to it.

---

## 2. The mental model

### Two people closing a book

Picture a sorted row of values and one person standing at each end, walking
toward each other. Neither is allowed to turn around.

```
        ┌─── want a pair summing to 13 ───┐

index:    0    1    2    3    4    5    6
        ┌────┬────┬────┬────┬────┬────┬────┐
values: │  1 │  3 │  4 │  6 │  8 │ 10 │ 14 │
        └────┴────┴────┴────┴────┴────┴────┘
          ▲                              ▲
          lo                             hi        1 + 14 = 15  → too big
                                                   only a SMALLER right can help
          ▲                         ▲
          lo                        hi             1 + 10 = 11  → too small
                                                   only a BIGGER left can help
               ▲                    ▲
               lo                   hi             3 + 10 = 13  → found
```

Look at what happened at the very first step. `1 + 14` was too big, so `hi`
moved left. In that single move we permanently eliminated **every pair involving
index 6** — because 14 was already paired with the *smallest* possible partner
and the sum was still too large. No other partner can rescue it.

Six pairs killed, one comparison. That's the whole technique. Each step discards
an entire row or column of the n² grid of pairs, so n steps cover n² pairs.

### The grid it's really searching

```
            hi →   1    3    4    6    8   10   14
        lo  ┌────────────────────────────────────┐
        ↓ 1 │              the whole grid of     │
          3 │              pairs is n²           │
          4 │                                    │
          6 │   two pointers walks ONE path      │
          8 │   through it, length n, and every  │
         10 │   step rules out a full line       │
         14 │                                    │
            └────────────────────────────────────┘
```

The hash map from Lesson 1 also avoided the n² grid, but by a different trick:
it *remembered* where it had been. Two pointers doesn't remember anything. It
doesn't need to, because sortedness tells it which direction is hopeless.

**Memory vs. monotonicity.** Both buy you the same factor of n. One costs RAM;
the other costs a sort, or costs nothing if the input arrives sorted.

---

## 3. The mechanics — enough to reason about cost

### Why "converging" is O(n) and not O(n²)

There is a nested-loop *shape* here — a `while` with two moving indices — and it
is easy to look at it and guess O(n²). It isn't, and you need to be able to say
why in one sentence:

> Every iteration of the loop moves `lo` right or `hi` left, never both backward.
> The gap `hi - lo` strictly decreases and starts at n, so there are at most n
> iterations.

That argument is called a **monotonic potential function** if you want the
formal name, but the plain version is fine: *something that can only shrink,
starting at n, shrinking every step*. Say it out loud. It's the difference
between an interviewer believing your complexity claim and probing it.

### What sortedness actually buys: monotonicity

Two pointers works when the quantity you're steering is **monotonic in each
pointer's movement**. For pair sums on a sorted array:

- moving `lo` right → the sum can only **increase**
- moving `hi` left → the sum can only **decrease**

So the sum is a dial with two knobs and you always know which way each turns.
When the sum is too small, exactly one move is possibly-useful. No search, no
backtracking, no choice to regret.

If that monotonicity doesn't hold, the technique is invalid — not slow, *wrong*.
A pointer move would discard candidates that might still have been answers.
**Before writing the loop, name the monotonic quantity.** If you can't name it,
you've picked the wrong tool.

### The cost of getting the input sorted

If the array isn't sorted, you sort it: **O(n log n)**, and that dominates the
O(n) scan. So:

| Input state | Total time | Extra space |
|---|---|---|
| Already sorted | O(n) | O(1) |
| Must sort first | O(n log n) | O(1) or O(n), depends on the sort |

Two things follow:

1. **Sorting is not free, but it's often cheap enough.** O(n log n) beats O(n²)
   comfortably. Don't refuse to sort just because a hash map is O(n) — check
   whether the problem actually needs the extra speed or needs the space back.
2. **Sorting destroys the original indices.** If the problem wants you to
   *return indices*, sorting has thrown away the answer. That single sentence is
   why Two Sum (which returns indices) is a hash-map problem and Three Sum
   (which returns values) is a sorting problem. Read the return type.

Python's `sorted()` is Timsort — O(n log n), stable, and it allocates a new
list (O(n) space). `list.sort()` sorts in place, which is how you keep the O(1)
space claim honest.

### Two flavours of two pointers

Both use two indices. They are not the same tool and confusing them is a common
failure:

```
  CONVERGING (this lesson)          SAME-DIRECTION (sliding window, Topic 3)
  ──────────────────────            ──────────────────────────────────────
   →                  ←              →      →
   lo ............... hi             lo ... hi
   start apart, meet in middle       both move right, never cross
   interest: the PAIR at the ends    interest: the RANGE between them
   trigger: pairs, triples,          trigger: subarrays, substrings,
            palindromes                       "longest/shortest window"
```

This topic is the left column. Topic 3 is the right column. Filing them
separately now saves confusion later.

---

## 4. Complexity — the table to know cold

| Approach | Time | Space | When it's right |
|---|---|---|---|
| Brute force, every pair | O(n²) | O(1) | Baseline only — always state it first |
| Brute force, every triple | O(n³) | O(1) | Baseline for Three Sum |
| Hash map / complement lookup | O(n) | **O(n)** | Unsorted input, and you need original indices |
| Converging pointers, sorted input | **O(n)** | **O(1)** | Sorted input, pair condition, monotonic |
| Sort, then converging pointers | O(n log n) | O(1)–O(n) | Unsorted, values (not indices) wanted |
| Sort, then fix-one + converging pair | **O(n²)** | O(1)–O(n) | Triples. The outer loop dominates the sort |

The last row is the one people get wrong. Fixing one element and running a
converging pair inside it is n × O(n) = **O(n²)**, and the O(n log n) sort
disappears into it. O(n²) sounds bad; for the triple-sum shape it is the
accepted optimal, and saying so confidently is part of the answer.

---

## 5. The patterns

Each is **a trigger in the problem statement → the tool**. Three patterns, one
per problem in the topic. Learn the triggers.

### Pattern A — converging pointers → [[../concepts/opposite-end-pointers|concept page]]
> **Trigger:** "palindrome", "reverse", "from both ends", "a pair such that…",
> "is it symmetric"

Start at both ends, walk inward, `while lo < hi`. The loop body compares or
combines the two ends, then moves one or both pointers based on what it found.

This is the base pattern; everything else in the topic is a variation on it. The
two decisions that define any instance:

- **What's the comparison at the ends?** Equality (palindrome), a sum (pair
  sum), an area (container).
- **Which pointer moves, and why?** This is the real content. A palindrome moves
  *both* on a match. A pair sum moves *exactly one*, chosen by which direction
  the sum needs to go.

**The filtering wrinkle.** Sometimes a pointer must skip elements that don't
count — non-alphanumeric characters, whitespace, already-processed values.
That's an inner `while` inside the outer `while`, and it is still O(n) total,
because the inner loop only ever advances the same pointers the outer loop
advances. Guard those inner loops with `lo < hi` too, or a string of all-skippable
characters walks the pointer off the end.

### Pattern B — sort, then scan → [[../concepts/sort-then-scan|concept page]]
> **Trigger:** the problem is about **values, not positions**; "find all triples",
> "group by closeness", "does any pair/triple satisfy…" on an unsorted array

Sorting is a preprocessing step that *creates* the monotonic structure the rest
of the algorithm needs. You pay O(n log n) once and every subsequent step gets
cheaper.

The checklist before you sort:

1. **Does the problem need the original indices?** If yes, you can't sort
   (or you must sort `(value, index)` pairs and carry the index along).
2. **Does the problem care about the original order?** Subarray and substring
   problems usually do — sorting is illegal there.
3. **Will the sort be dominated anyway?** If the rest is O(n²), the sort is free
   in big-O terms. Take it.

### Pattern C — fix one, converge on the rest
> **Trigger:** "three numbers", "triples", "a + b + c = target" — any k-sum with
> k > 2

Reduce the problem by one dimension. Freeze the outermost element, and the
remaining question is a pair problem on the suffix — which is Pattern A. A
triple-sum becomes n instances of a pair-sum: O(n) × O(n) = O(n²).

```
sorted:  [ -4, -1, -1,  0,  1,  2 ]
            ▲   ▲                ▲
          fixed lo               hi     ← "find a pair summing to +4"
                                          in the region right of the fixed one

          then advance the fixed element and do it again
```

Two details are where this problem is actually lost, and neither is the
algorithm:

- **The pair search must run to the *right* of the fixed element only.** Starting
  `lo` at 0 lets a triple use the same element twice and produces every
  permutation of every answer.
- **Duplicates must be skipped, in two places** — on the fixed element and on
  both pointers after a hit. See [[../concepts/duplicate-skipping|duplicate
  skipping]]. This is the single most common reason a Three Sum submission
  fails, and it fails on *correctness*, not speed.

### Pattern D — greedy converging on a trade-off
> **Trigger:** two quantities in tension where one is fixed by *position* and the
> other by *value* — "maximum area", "widest/tallest", "best pair under a
> constraint"

A variant worth its own entry, because the move rule is not "steer toward a
target" — there's no target. It's **"discard the pointer that can't possibly
improve."**

The shape: your objective depends on the *distance* between the pointers and on
the *smaller* of the two values. Start maximally wide. Every move inward costs
you width, guaranteed — so the only way a move can pay off is by improving the
value, and only the *limiting* (smaller) side can improve. So: move the smaller
side inward; the larger side stays.

The argument you must be able to give: *the pointer at the smaller value has
already been tested against its widest possible partner. Keeping it can only
produce narrower configurations with the same limiting value — all strictly
worse. Discarding it discards nothing that could have won.*

That is a genuine **greedy exchange argument**, and an interviewer may well ask
for it. "It works, I tried it" is not an answer. Being able to say *why no
discarded candidate could have been optimal* is the difference between a pass
and a "he got it but couldn't justify it."

---

## 6. Python notes

```python
lo, hi = 0, len(nums) - 1
while lo < hi:                 # lo < hi: the two must be distinct elements
    ...                        # lo <= hi would let an element pair with itself
    lo += 1
    hi -= 1

nums.sort()                    # in place, O(1) extra space — keeps the claim honest
ordered = sorted(nums)         # new list, O(n) space — fine, but say so

s[::-1]                        # reverse a string. O(n) SPACE — a real copy.
                               # "reverse it and compare" is O(n) space; two
                               # pointers is O(1). Know which one you claimed.

ch.isalnum()                   # letters or digits — the filter for Valid Palindrome
ch.lower()                     # case folding. Do it per-character, not on a copy,
                               # if you want to keep O(1) space

while lo < hi and not s[lo].isalnum():   # ALWAYS re-check lo < hi in the inner
    lo += 1                              # loop, or an all-punctuation string
                                         # runs the pointer off the end
```

**On `while lo < hi` vs `while lo <= hi`:** for pair problems it is `<`, because
a pair needs two distinct positions. For palindromes `<` is also right — a
middle character in an odd-length string is trivially equal to itself, so there's
nothing to check. Getting this wrong doesn't usually crash; it silently returns
a wrong answer on one edge case. Decide it deliberately, every time.

**One more cost to know:** slicing a list or string in Python (`nums[i:]`,
`s[1:-1]`) **copies**. Writing a "two pointer" solution that slices inside the
loop is O(n²) time and O(n) space wearing an O(n) costume. Move indices, not
slices.

---

## 7. Traps

- **Using two pointers on unsorted input.** The move rule is only valid under
  monotonicity, which sortedness provides. On unsorted data the code runs, looks
  plausible, and is wrong. Name the monotonic quantity before you write the loop.
- **Sorting when the answer is indices.** Two Sum wants indices; sorting loses
  them. Read the return type before you reach for `.sort()`.
- **Claiming O(1) space after `sorted()`.** `sorted()` allocates; `.sort()`
  doesn't. Also, CPython's Timsort uses O(n) auxiliary space in the worst case
  even in-place — if you want to be strictly correct, say "O(1) beyond the sort."
- **Forgetting duplicate skipping in the triple-sum shape.** Produces duplicate
  answers, fails the test, and is not a complexity problem — it's a correctness
  problem, which reads much worse.
- **Skipping duplicates in only one of the two places.** The fixed element *and*
  the pointers after a successful hit. One without the other still leaks dupes.
- **Unguarded inner skip loops.** `while not s[lo].isalnum(): lo += 1` with no
  `lo < hi` guard walks off the end on `",.;"`. Classic IndexError in front of an
  interviewer.
- **Moving both pointers when only one should move.** In a pair sum you move
  exactly one, chosen by the comparison. Moving both skips candidate pairs.
- **Slicing inside the loop.** Silent O(n²). Indices only.
- **Not stating the brute force first.** O(n²) for pairs, O(n³) for triples. Say
  it, then improve on it. Jumping straight to the clever answer reads as
  memorised, and interviewers probe memorised answers hardest.
- **Empty and single-element inputs.** `[]`, `[x]`, `""`, a string with no
  alphanumeric characters at all. Check before declaring done — this was flagged
  in Topic 1 and applies unchanged.

---

## 8. Worked example

Outside the problem set, so nothing is spoiled.

> **Problem.** Given an array of integers **sorted in non-decreasing order**,
> return an array of the squares of each number, also sorted in non-decreasing
> order. `[-4, -1, 0, 3, 10]` → `[0, 1, 9, 16, 100]`.

**Brute force.** Square everything, then sort. O(n log n). Say this first — it's
correct, it's simple, and it sets the bar the clever answer has to beat.

**Spot the tension.** The input is already sorted, but squaring breaks the
order: negatives flip. A large negative squares to a large positive. So the
*largest* square is at one end or the other — never in the middle.

That last sentence is the trigger. **The extremes of the answer live at the
extremes of the input.** Pattern A: start at both ends.

The monotonic quantity: as `lo` moves right, `|nums[lo]|` only decreases; as
`hi` moves left, `|nums[hi]|` only decreases. So the biggest remaining square is
always at one of the two current ends — which means we can fill the output array
from the back, largest first.

```python
def sorted_squares(nums):
    n = len(nums)
    result = [0] * n
    lo, hi = 0, n - 1
    for write in range(n - 1, -1, -1):     # fill from the back: biggest first
        left_sq, right_sq = nums[lo] ** 2, nums[hi] ** 2
        if left_sq > right_sq:
            result[write] = left_sq
            lo += 1
        else:
            result[write] = right_sq
            hi -= 1
    return result
```

Note `lo` and `hi` are allowed to meet here — the loop runs exactly n times and
the final iteration has `lo == hi`, writing the single smallest square. That's a
*different* boundary condition from the `while lo < hi` pair loop, and it's
deliberate: here we're consuming every element, not pairing them.

**Complexity.** O(n) time, O(1) extra space (the output doesn't count). We beat
the sort by spending the structure that was already in the input.

**The move to internalise:** the brute force threw away the input's sortedness
and then paid O(n log n) to buy it back. Any time you find yourself sorting
something that arrived sorted, stop — there is almost certainly a linear pass
hiding in the structure you just discarded.

---

## 9. Materials

Full list on [[../meta/resources|Resources]]. In order:

1. [Two pointers technique](https://www.geeksforgeeks.org/two-pointers-technique/)
   — 10 min. The canonical write-up; read it for the converging case and ignore
   the sliding-window sections for now.
2. [Sorting algorithms](https://www.geeksforgeeks.org/sorting-algorithms/) —
   skim. You don't need to implement one, but you should know O(n log n) is the
   comparison-sort floor and why.
3. [Python complexity cheat sheet](https://www.geeksforgeeks.org/complexity-cheat-sheet-for-python-operations/)
   — back to it for `sort` vs `sorted`, and for slicing costs.
4. Python docs: [Sorting HOW TO](https://docs.python.org/3/howto/sorting.html) —
   `key=`, stability, in-place vs. copy.
5. [NeetCode on YouTube](https://www.youtube.com/@NeetCode) — search the problem
   name, but **only after** a timed attempt and a debrief.
6. Optional extra reps if a pattern won't stick:
   [LeetCode 75](https://leetcode.com/studyplan/leetcode-75/) has a two-pointers
   section.

---

## 10. Self-check

Aloud, in full sentences, before the timer starts.

1. Why is a converging two-pointer loop O(n) and not O(n²)? Give the shrinking-
   quantity argument.
2. What property must the input have for the technique to be *valid*, not just
   fast? What breaks if it doesn't hold?
3. Two Sum on an unsorted array: hash map or two pointers? Now the array is
   sorted — does your answer change? What if the problem asks for indices
   instead of values?
4. What does sorting cost, and when is that cost invisible in the final big-O?
5. What's the difference between `nums.sort()` and `sorted(nums)` in space terms?
6. When is it `while lo < hi` and when is it `while lo <= hi`?
7. For the triple-sum shape: what's the overall complexity, and why doesn't the
   sort appear in it?
8. In the max-area shape, why is it safe to discard the pointer at the smaller
   value? Give the argument, not the result.
9. Give the trigger phrase for each of patterns A–D.
10. Name the two flavours of two pointers and the different trigger for each.

---

## 11. Ready?

**First timed problem: Valid Palindrome** (Easy) — Pattern A.

Say `timed valid-palindrome` and the 30-minute box starts. No hints while it's
running.

**What "good" looks like:**

- You ask the clarifying questions before writing anything — what counts as a
  character, does case matter, what about an empty string
- You reach for converging pointers without first writing the `s[::-1]` version,
  or if you do write it, you name its O(n) space cost immediately and then
  improve it
- Your inner skip loops are guarded with `lo < hi`
- You test `""`, `"a"`, and a string of pure punctuation before saying done
- You state time **and** space, and you can defend the O(n) time claim with the
  shrinking-gap argument

Topic 1's lesson was that finishing fast isn't the win condition — 7 minutes used
of 30 leaves 23 on the table. Same rule here. If Valid Palindrome falls quickly,
roll straight into **Three Sum** with what's left; you won't finish it, and that's
fine. Getting 20 minutes of real thought onto the hardest problem in the topic is
worth more than a clean early finish.

Three Sum is a genuine step up and it is normal for it to take several attempts.
It goes in the [[../meta/review-queue|review queue]] at +2 days and comes back
until it's cold. **Repeat until confident is the default, not the fallback.**
