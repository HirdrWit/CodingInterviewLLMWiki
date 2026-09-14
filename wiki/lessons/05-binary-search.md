---
type: lesson
topic: binary-search
status: ready
updated: 2026-09-14
sources: [https://www.geeksforgeeks.org/binary-search/, https://docs.python.org/3/library/bisect.html]
tags: [lesson, binary-search]
---

# Lesson 5 — Binary Search

**Curriculum:** [[../curriculum/binary-search|Topic 5]] · **Skill:** [[../skills/algorithms|Algorithms]] · **Materials:** [[../meta/resources|Resources]]
**Work through in:** 60–90 minutes. Only 2 problems in this topic, and both are
Mediums. The lesson is longer than the problem count suggests because the
difficulty here is entirely in the details, not the idea.

---

## 1. Why this topic

The obvious reason: you have a sorted array, you want O(log n) instead of O(n).
Fine. That version of binary search is four lines long and nobody gets asked it.

The real reason is that **binary search is the first algorithm in the curriculum
that is not a scan.** Everything up to now walked the input: the
[[../concepts/seen-set|seen-set]] walked it once, [[../concepts/two-pointers|two
pointers]] walked it from both ends, sliding window walked it with a rubber band
attached. All of them touch every element, and all of them are O(n).

Binary search is the first tool that **throws away half the input without
looking at it.** That is a fundamentally different move, and it needs a
fundamentally different justification: you may only discard the half you can
*prove* contains no answer. The proof comes from a **monotonic predicate** over
the search space — some property that is false, false, false, then true, true,
true, and never flips back.

Once you see it that way, the sorted array is revealed as a special case. The
predicate `nums[i] >= target` happens to be monotonic *because* the array is
sorted. But you can binary search anything with that shape — a rotated array, a
range of possible answers, a function you can only sample. That generalisation
is Topic 5's real payload, and it comes back in Heap problems, in Intervals, and
in every "minimise the maximum" question you will ever be asked.

The two Blind 75 problems here are both about the same provocation: **what
happens when the sort invariant is broken but not destroyed?** A rotated sorted
array isn't sorted, so the naive predicate fails. But it's still made of two
sorted runs, and that is enough structure to keep halving. Learning to find the
surviving invariant in damaged data is the transferable skill.

---

## 2. The mental model

### Guessing a number between 1 and 100

Someone picks a number. After each guess they say "higher" or "lower". You do
not guess 1, 2, 3. You guess 50, and whatever they answer, **half the
possibilities are gone forever.** 100 → 50 → 25 → 13 → 7 → 4 → 2 → 1. Seven
guesses covers a hundred numbers; twenty covers a million.

That's the whole algorithm. The interesting question is never "how do I halve" —
it's **"what am I entitled to throw away?"**

### The picture: a range that shrinks from both ends

Hold two fences, `lo` and `hi`. Everything outside them has been ruled out.
Everything between them is still in play. Each step moves one fence past the
midpoint.

```
step 0   lo─────────────────────────────hi        32 candidates
         ┌──────────────┬──────────────┐
         │   discard    │    keep      │          probe the middle,
         └──────────────┴──────────────┘          decide which side can't hold the answer

step 1                  lo──────────────hi        16
step 2                  lo──────hi                 8
step 3                      lo──hi                 4
step 4                       lo─hi                 2
step 5                        lo=hi                1  ← answer
```

The window never grows and never skips. **The answer, if it exists, is always
inside `[lo, hi]`** — that sentence is the loop invariant, and keeping it true
is the entire job.

### The predicate picture

Forget arrays for a second. Draw the search space as a row of cells and mark
each one with whether some yes/no question holds there:

```
space:      0    1    2    3    4    5    6    7    8
P(i):       F    F    F    F    T    T    T    T    T
                                ↑
                     the boundary — the first True
```

Binary search finds **that boundary**. Not "an element". A boundary. The only
requirement is that the F's all come before the T's — the predicate is
**monotonic**. If the pattern were `F T F T T`, probing the middle tells you
nothing, and no amount of cleverness recovers it.

So the real question when you meet a problem is:

> *Is there a yes/no question about a position whose answer flips exactly once
> across the space?*

If yes, binary search applies, whether or not anything is sorted.

---

## 3. The mechanics — enough to reason about cost

**Why log n.** Each iteration multiplies the candidate count by ½. Starting at
n, after k steps you have n/2^k candidates. You stop at 1, so n/2^k = 1, so
k = log₂ n. For n = 1,000,000 that's 20 iterations. This is why binary search
appears the instant a problem says "n up to 10^9" — an O(n) scan is dead at that
size and O(log n) doesn't even notice.

**Why the midpoint and not some other split.** Any fixed fraction gives you
logarithmic behaviour (thirds give log₃ n, which is the same up to a constant).
Halving is optimal against an adversary: it minimises the *worst* remaining
side. If you probe at 10%, an adversary always answers so you keep the 90%.

**`mid = lo + (hi - lo) // 2` and why.** The obvious `(lo + hi) // 2` can
overflow in a fixed-width integer language when `lo` and `hi` are both near the
maximum — a bug that famously sat in the JDK's binary search for nine years.
Python integers are arbitrary precision so it cannot actually overflow here,
**but write it the safe way anyway**: it costs nothing, and an interviewer who
knows the history will notice. `hi - lo` is the width of the live window, always
small; adding half of it to `lo` can never exceed `hi`.

Note also that `//` in Python floors, so `mid` leans **toward `lo`**. That
asymmetry is the source of most infinite loops — see the traps.

**Random access is required.** Binary search needs `arr[mid]` in O(1). On a
linked list, finding the middle is itself O(n), which destroys the whole point.
This is a real interviewer follow-up: *"could you binary search a linked list?"*
Answer: not usefully — you'd pay O(n) per probe for O(log n) probes.

**The cost of getting sorted.** If the input isn't sorted and you sort it just to
binary search once, you paid O(n log n) to save… less than that. Sorting to
binary search only pays off when you will search **many** times, or when sorting
buys you something else too.

---

## 4. Complexity

| Operation | Time | Space | Why |
|---|---|---|---|
| Binary search, iterative | **O(log n)** | **O(1)** | halving; two integer variables |
| Binary search, recursive | O(log n) | O(log n) | call stack depth = number of halvings |
| Binary search on an answer range of size R | O(log R × cost of the check) | O(1) | the space is the *range*, not the array |
| Linear scan (the baseline you beat) | O(n) | O(1) | — |
| Sort, then binary search once | O(n log n) | O(n) or O(1) | the sort dominates — usually not worth it |
| Hash lookup (the rival) | O(1) avg | O(n) | faster, but loses all order information |

The row to internalise is the last one. **A hash map beats binary search on pure
membership.** Binary search wins when you need something a hash map cannot give
you: the *nearest* value, the *first* value ≥ x, the boundary of a range, the
minimum of something. Order questions, not presence questions. That fork —
presence → hash, order → binary search — is the actual decision.

---

## 5. The patterns

Each is **a trigger in the problem statement → the tool**. The trigger half is
what transfers.

### Pattern A — the classic template → [[../concepts/binary-search|concept page]]
> **Trigger:** "sorted array", "O(log n) required", "n up to 10^9"

Memorise exactly one template and write it without thinking. This is the
`while lo < hi` form that converges on a boundary, and it is the one worth
burning in because it generalises to every other pattern below:

```python
lo, hi = 0, len(nums) - 1        # invariant: answer is inside [lo, hi]
while lo < hi:
    mid = lo + (hi - lo) // 2
    if condition(mid):
        hi = mid                 # mid might BE the answer — keep it
    else:
        lo = mid + 1             # mid is definitively ruled out — discard it
return lo                        # lo == hi, one candidate left
```

Read the two branches as a pair. One of them *must* exclude `mid`, or the window
stops shrinking and you loop forever. Here `lo = mid + 1` is the excluding
branch, which is what makes the floor-division `mid` safe.

The other form, `while lo <= hi` with `hi = mid - 1`, is for *"find this exact
value or report absent"*. Both are correct; pick one as your default and only
reach for the other deliberately. See
[[../concepts/loop-invariant-bounds|loop-invariant bounds]] for the full
comparison.

### Pattern B — binary search on the answer, not the array → [[../concepts/search-space-invariant|concept page]]
> **Trigger:** "minimise the maximum", "maximum possible minimum", "smallest k such that…", "the least capacity/speed/size that works"

The array may be unsorted and irrelevant. The thing you binary search is the
**range of possible answers**. Define `feasible(x)` = "does x work?". If
`feasible` is monotonic — x works ⟹ every larger x also works — then the answers
look `F F F T T T` and you binary search for the boundary.

```
candidate answers:  1    2    3    4    5    6    7    8
feasible(x):        F    F    F    T    T    T    T    T
                                  ↑ the answer
```

The array only appears inside `feasible`, as a linear check. Total cost is
O(n log R) where R is the range of answers. **This is the pattern that most
often looks like a completely different problem**, and recognising it is worth
more than anything else in this lesson.

### Pattern C — the broken invariant
> **Trigger:** "rotated sorted array", "sorted but shifted", "sorted array with one anomaly"

A rotated sorted array is not sorted, so `nums[mid] > target` tells you nothing
on its own. But it is **two sorted runs**, and that is enough:

```
original:  [ 0  1  2  4  5  6  7 ]
rotated:   [ 4  5  6  7  0  1  2 ]
             └────┬────┘ └──┬──┘
              run A (high)  run B (low)     every A > every B
```

The recoverable invariant is: **at least one side of any midpoint is a clean
sorted run.** Compare `nums[mid]` against an endpoint to find out which side is
clean; then, on that side, you can reason normally. The other side you either
discard or recurse into. The general lesson — *when the obvious invariant
breaks, look for the weaker one that survives* — is the whole reason these two
problems are in the Blind 75.

### Pattern D — finding a boundary, not a value
> **Trigger:** "first occurrence", "last occurrence", "insertion point", "how many are less than x", "the minimum element"

There may be duplicates, or the value may not exist at all, and you want the
*edge* of a region rather than a hit. Do not try to find the value and then walk
left — that walk is O(n) and defeats the purpose. Instead **change the
predicate**: search for the first index where `nums[i] >= target`. The template
in Pattern A returns that boundary directly. Counting "how many are less than x"
is then just the returned index.

This reframe — *from finding a value to finding a boundary* — is the single most
useful habit in this topic. Most binary search bugs are people trying to make a
value-finding template answer a boundary question.

### Pattern E — searching a 2-D grid as a flat array
> **Trigger:** "m × n matrix, each row sorted, first element of each row greater than the last of the previous"

Such a matrix *is* a sorted array of length m×n that happens to be stored in
rows. Binary search indices `0 .. m*n-1` and convert on the fly:
`row, col = divmod(idx, n)`. O(log(mn)). Worth knowing because the trigger is
wordy and people miss that the wordiness is describing "it's just sorted".

---

## 6. Python notes

```python
import bisect

bisect.bisect_left(a, x)      # first index where a[i] >= x   ← the boundary, Pattern D
bisect.bisect_right(a, x)     # first index where a[i] >  x
bisect.insort(a, x)           # insert keeping sorted order — O(n), the insert dominates

# count of elements equal to x, in O(log n):
bisect.bisect_right(a, x) - bisect.bisect_left(a, x)
```

`bisect` is the correct answer in production code and you should say so out
loud. Then write it by hand anyway, because the interview is testing whether you
can. The two names are worth memorising precisely: **`bisect_left` gives you the
leftmost insertion point, which is the first index ≥ x** — that is Pattern D for
free. See the [`bisect` docs](https://docs.python.org/3/library/bisect.html).

Two more:

```python
mid = lo + (hi - lo) // 2     # write it this way, always
lo, hi = 0, len(nums) - 1     # index bounds — inclusive on both ends
lo, hi = 0, len(nums)         # insertion-point bounds — hi is one PAST the end
```

Those last two lines are different algorithms. Decide which one you're writing
*before* you type the loop, and say which out loud. And beware: Python's `//`
floors toward negative infinity, so with negative bounds (rare, but it happens
on answer-space searches) `-3 // 2 == -2`, not `-1`.

---

## 7. Traps

Off-by-ones are not *a* difficulty in this topic — they are the *entire*
difficulty. The idea takes ten seconds to explain and the implementation takes
people years to get reliable. Every item below has cost someone an offer.

- **The infinite loop.** `while lo < hi` with `lo = mid` in one branch. When
  `hi == lo + 1`, floor division gives `mid == lo`, so `lo = mid` changes
  nothing and the loop spins forever. **Rule: because `mid` floors toward `lo`,
  the branch that moves `lo` must be `lo = mid + 1`.** If your logic genuinely
  needs `lo = mid`, you must switch to the ceiling midpoint
  `mid = lo + (hi - lo + 1) // 2`. Never mix: floor-mid pairs with `lo = mid+1`,
  ceil-mid pairs with `hi = mid-1`.

- **`<` versus `<=` chosen by feel.** They mean different things.
  `while lo < hi` exits with **one candidate left** (`lo == hi`) — use it when an
  answer definitely exists and you want *which one*. `while lo <= hi` exits with
  **zero candidates** (`lo > hi`) — use it when the target may be absent and you
  need to return `-1`. Picking the wrong one is how you get a correct-looking
  function that returns the wrong thing on the two-element case.

- **Inclusive/exclusive `hi` drift.** Set `hi = len(nums) - 1` and then write
  `hi = mid - 1` somewhere and `hi = mid` somewhere else in the same function and
  it is over. **Write down what `hi` means before the loop** — "last index still
  possible" or "one past the last possible" — and never let a line contradict it.
  That sentence is [[../concepts/loop-invariant-bounds|the loop invariant]].

- **Returning `mid` from inside the loop when the question is a boundary.**
  With duplicates, the `mid` you hit is an arbitrary member of the run, not the
  first. If the problem says "first" or "last", you must converge, not return
  early. See Pattern D.

- **Off-by-one at the two-element window.** `[a, b]` is where every binary
  search bug lives. When you finish writing, **trace `n = 2` by hand** — then
  `n = 1`, then `n = 0`. Three traces, thirty seconds, and it catches almost
  everything. Do this before you say you're done, not after the interviewer asks.

- **Assuming sortedness the problem didn't give you.** Rotated is not sorted.
  "Non-decreasing" allows duplicates and duplicates break the rotated-array
  reasoning (with `nums[lo] == nums[mid] == nums[hi]` you cannot tell which side
  is clean, and worst case degrades to O(n)). If the constraints say "all values
  distinct", **say out loud that you are relying on it** — it's exactly the
  follow-up they want to ask.

- **Comparing against the wrong anchor.** In a rotated array, comparing
  `nums[mid]` to `nums[hi]` and comparing it to `nums[lo]` lead to different —
  both valid — case analyses. Mixing the two mid-solution produces code that
  passes half the tests. Pick your anchor, write it in a comment, stick to it.

- **Forgetting the predicate must be monotonic.** Binary search on an answer
  range is only correct if feasibility never flips back. If you cannot say in
  one sentence *why* `feasible(x) ⟹ feasible(x+1)`, you do not yet have a
  binary search — you have a guess. Interviewers ask for this justification.

- **Claiming O(log n) when the check inside is O(n).** Pattern B is
  O(n log R), not O(log R). State both factors.

---

## 8. Worked example

Not from the problem set, so nothing is spoiled. This one is Pattern B, because
Pattern B is the one that's hardest to recognise cold.

> **Problem.** *Koko Eating Bananas.* There are `n` piles of bananas,
> `piles[i]` in pile `i`, and `h` hours before the guards return. Koko picks an
> eating speed `k` bananas/hour. Each hour she picks one pile and eats up to `k`
> from it; if the pile has fewer than `k` left, she finishes it and eats no more
> that hour. Return the **minimum** `k` that lets her finish all piles within
> `h` hours.
> `piles = [3, 6, 7, 11]`, `h = 8` → `4`.

**Brute force.** Try k = 1, 2, 3, … and stop at the first one that works.
Checking one k costs O(n). The largest k worth trying is `max(piles)`, so this
is O(n × max(piles)) — with piles up to 10^9, hopeless. Say this out loud first
anyway; it's how you find the search space.

**Spot the pattern.** There is a sorted array nowhere in sight. But look at what
brute force is doing: scanning candidate answers 1, 2, 3, … in order, looking
for the first one that works. That's a **linear scan over an ordered space** —
exactly what binary search replaces.

The thing to verify — and to say aloud, because it is the actual insight — is
**monotonicity**: if speed `k` finishes in time, then `k+1` certainly does, since
eating faster never takes longer. So feasibility looks `F F F T T T` and has one
boundary. That is the licence to binary search.

```python
import math

def min_eating_speed(piles, h):
    def hours_needed(k):                      # O(n)
        return sum(math.ceil(p / k) for p in piles)

    lo, hi = 1, max(piles)                    # invariant: answer lies in [lo, hi]
    while lo < hi:                            # converge to one candidate
        mid = lo + (hi - lo) // 2
        if hours_needed(mid) <= h:            # mid is feasible...
            hi = mid                          # ...but maybe smaller works — KEEP mid
        else:
            lo = mid + 1                      # mid too slow — definitively discard
    return lo
```

**Check the bounds.** `lo = 1` because speed 0 eats nothing. `hi = max(piles)`
because at that speed every pile takes exactly one hour, which is the best any
larger speed can do — so no answer is ever above it. The problem guarantees
`h >= n`, so a feasible answer exists inside the range, which is what justifies
`while lo < hi` rather than the `<=` form.

**Complexity.** O(n log(max(piles))). Say both factors: the log is the number of
speeds tried, the n is the cost of testing one. Space O(1).

**The move to internalise:** the brute force was *scanning candidate answers in
order*. The moment you see that — and can argue the feasibility test is
monotonic — the answer range becomes the array and binary search applies. Nothing
in the input was ever sorted.

---

## 9. Materials

Full list on [[../meta/resources|Resources]]. For this lesson, in order:

1. [Binary search](https://www.geeksforgeeks.org/binary-search/) — the base
   algorithm, iterative and recursive. 15 min.
2. [Python `bisect` docs](https://docs.python.org/3/library/bisect.html) — short,
   and `bisect_left` is Pattern D handed to you.
3. Search GeeksforGeeks for *"binary search on answer"* — the Pattern B write-up
   under that name is the one worth reading twice.
4. [NeetCode on YouTube](https://www.youtube.com/@NeetCode) — the Binary Search
   playlist, but **only after** a timed attempt on a given problem.
5. Optional extra reps if the off-by-ones won't stick:
   [LeetCode 75](https://leetcode.com/studyplan/leetcode-75/) has a binary search
   section with gentler problems than the two here.

---

## 10. Self-check

Aloud, in full sentences, before the timer starts.

1. What must be true of a search space before binary search is valid? Say it
   without using the word "sorted".
2. Why is `mid = lo + (hi - lo) // 2` preferred over `(lo + hi) // 2`, and does
   it matter in Python?
3. Write the `while lo < hi` template from memory. Now say what `lo` and `hi`
   *mean* at every point in the loop.
4. When does `while lo < hi` exit, and when does `while lo <= hi` exit? Which do
   you use when the target might be absent?
5. Why does `lo = mid` cause an infinite loop, and what are the two ways to fix
   it?
6. A problem says "find the first index where `nums[i] >= target`". Why is
   "find the target, then walk left" wrong?
7. A rotated sorted array is not sorted. What property does it still have that
   makes halving legal?
8. What does `bisect_left` return, exactly?
9. Give the trigger phrase for each of patterns A–E.
10. You binary search an answer range of size R with an O(n) feasibility check.
    State the complexity.

---

## 11. Ready?

**First timed problem: Find Minimum in Rotated Sorted Array** (Medium) —
Pattern C.

Say `timed find-minimum-in-rotated-sorted-array` and I'll give you the statement
and start the 30-minute box. No hints while it's running.

**What "good" looks like here.** This is your first Medium-only topic, so the
bar is about *process*, not speed:

- You state the brute force (scan for the minimum, O(n)) and its complexity
  before writing anything clever
- You write down what `lo` and `hi` mean **as a comment, before the loop body**
- You say aloud which anchor you're comparing `nums[mid]` against, and you don't
  change your mind halfway
- You use `lo + (hi - lo) // 2` without being prompted
- **You hand-trace `n = 1` and `n = 2` before declaring done.** This is the
  non-negotiable one for this topic.
- You state O(log n) time, O(1) space, and you mention what duplicates would do
  to your reasoning

If you finish well inside 30 minutes, do **not** roll straight into problem 2.
Instead, write your template out from memory on a blank page and trace it on
`[1]`, `[2,1]`, and `[3,4,5,1,2]`. The second problem is the same machinery with
a harder case analysis, and it will go much better on a template you trust.

Not finishing is expected on Mediums. It goes in the
[[../meta/review-queue|review queue]] at +2 days and comes back.
**Repeat until cold-solvable is the plan, not the fallback.**
