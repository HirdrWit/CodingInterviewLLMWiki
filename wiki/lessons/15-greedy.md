---
type: lesson
topic: greedy
status: ready
updated: 2026-09-14
sources: [https://www.geeksforgeeks.org/greedy-algorithms/, https://www.geeksforgeeks.org/largest-sum-contiguous-subarray/, https://www.geeksforgeeks.org/introduction-to-exchange-argument/]
tags: [lesson, greedy]
---

# Lesson 15 — Greedy

**Curriculum:** [[../curriculum/greedy|Topic 15]] · **Skill:** [[../skills/algorithms|Algorithms]] · **Materials:** [[../meta/resources|Resources]]
**Prerequisites:** [[13-1d-dynamic-programming|Lesson 13 — 1-D DP]]. This topic is
defined *against* that one; without it, half of what follows has nothing to push on.
**Work through in:** 45–60 minutes. Short topic, two problems — but the thinking
here is the least mechanical in the whole curriculum.

---

## 1. Why this topic

The obvious reason: two problems, both Mediums, both extremely common in phone
screens. Maximum Subarray in particular is close to a rite of passage.

The real reason is that **greedy is the only topic in the Blind 75 where the
hard part is not the algorithm — it's the argument that the algorithm is
allowed.** Everywhere else, once you spot the pattern, correctness comes free:
a BFS visits every node, a DP table fills every subproblem, a binary search
provably halves the space. Greedy code is usually five lines and obviously
*fast*. Whether it's *right* is a separate question that the code cannot answer.

That makes this topic the one that trains a different muscle: stating a claim
about your own solution and then actively trying to break it. Interviewers
probe greedy solutions harder than any other kind, because a wrong greedy looks
exactly like a right greedy. The candidates who pass are the ones who say the
claim out loud before being asked.

Topic 13 taught you to consider every choice and remember the results. This
topic teaches you when you're allowed to skip all that — and, just as
importantly, when you're not.

---

## 2. The mental model

### DP is a chess player. Greedy is a hiker.

A chess player looks ahead down every branch, evaluates the positions, and backs
the best line up to the present move. That's [[13-1d-dynamic-programming|dynamic
programming]]: explore all the choices, memoise what each is worth, pick from
full information.

A hiker trying to reach the summit in fog uses a simpler rule: **from where I'm
standing, take the steepest upward step.** No lookahead, no backtracking, no
memory of the paths not taken. One decision per position, made from local
information, never revised.

```
        DP: keeps the whole tree                 GREEDY: keeps one path
             ┌───┐                                    ┌───┐
             │ • │                                    │ • │
           ┌─┴─┬─┴─┐                                  └─┬─┘
         ┌─┴─┐   ┌─┴─┐                                  │  "steepest step"
       ┌─┴┬┴─┐ ┌─┴┬┴─┐                                ┌─┴─┐
       …  …  … …  …  …                                └─┬─┘
                                                        │
   cost: every branch evaluated                       ┌─┴─┐
   memory: a table of answers                         └───┘

                                                cost: one pass
                                                memory: a couple of variables
```

The hiker is faster and carries nothing. The hiker also **walks confidently onto
a foothill and stops there**, while the real summit sits behind a dip the hiker
refused to descend into.

That foothill is the entire subject. Greedy is correct exactly when the terrain
has no foothills — when the locally steepest step is always on the path to the
global maximum. That property has a name: the **greedy-choice property**
([[../concepts/greedy-choice|concept page]]). It is a property of the *problem*,
not of your code, and it is either there or it isn't.

---

## 3. The mechanics — what has to be true

A greedy algorithm is licensed by two properties. Both are worth being able to
name, because "why is greedy safe here?" is a standard follow-up.

**1. The greedy-choice property.** There exists an optimal solution that
contains the locally-best choice. Not *every* optimal solution — one is enough.
This is what lets you commit without lookahead: having taken the greedy step,
you have not ruled out reaching an optimum.

**2. Optimal substructure.** After making that choice, what's left is a smaller
instance of the same problem, and solving *it* optimally completes an optimal
whole. DP needs this too — it's the shared half. **Greedy-choice is the part DP
does not need**, and the part that fails.

### The standard proof shape: the exchange argument

Assume an optimal solution `OPT` that does *not* start with your greedy choice
`g`. Show you can swap `g` into it without making it worse. Then `OPT` with `g`
is also optimal, so a greedy start was safe. Induct.

You will almost never produce a full exchange argument in a 45-minute interview,
and you are not expected to. What you *are* expected to do is:

> State the greedy claim in one sentence, then spend thirty seconds honestly
> trying to construct an input that breaks it.

That's the practical move. Say it out loud: *"I'm claiming that always taking
the largest coin is safe. Let me try to break that…"* Thirty seconds of genuine
adversarial thinking catches most wrong greedies, and the interviewer sees you
doing it.

### The classic failure: coin change

Make 6 from denominations `{1, 3, 4}`, using the fewest coins.

```
greedy — always take the largest coin that fits:

    6  →  take 4   (remaining 2)
    2  →  take 1   (remaining 1)
    1  →  take 1   (remaining 0)
                                    3 coins:  4 + 1 + 1

optimal:
                                    2 coins:  3 + 3
```

The greedy step `4` was locally the best possible and globally wrong: taking it
destroyed the `3 + 3` structure. No amount of careful coding fixes this, because
the problem does not have the greedy-choice property for that denomination set.

Note that with US denominations `{1, 5, 10, 25}` the same greedy *is* optimal.
**So "greedy works for coin change" is not a fact about coin change — it is a
fact about a particular set of coins.** Change the input distribution and the
algorithm silently stops being correct. That is the characteristic danger of
greedy and the reason interviewers push on it.

This is also exactly why **Coin Change lives in
[[13-1d-dynamic-programming|Topic 13]] and not here.** When greedy fails, DP is
the fallback: consider every coin at every amount, remember the results. Slower,
more memory, always right.

### How to tell, in practice

No test decides this for you. But these are the reliable tells:

| Signal | Leans |
|---|---|
| Asks for a **single number** — a max, a min, a count, a yes/no | greedy plausible |
| Asks you to **reconstruct the actual combination** | DP more likely |
| Every element must be *passed*, and passing it has one obvious best local action | greedy plausible |
| Choices **interact** — taking A changes what B is worth | greedy suspect |
| There's a **capacity or budget** being divided among items | greedy usually wrong |
| Sorting the input first makes an obvious rule appear | greedy plausible |
| The brute force is a decision tree with overlapping subtrees | DP |

Use them to form a hypothesis, then attack the hypothesis. Never skip the attack.

---

## 4. Complexity

| Approach | Time | Space | Why |
|---|---|---|---|
| Brute force over all subarrays / all paths | O(n²) or O(2ⁿ) | O(1)–O(n) | Every choice combination examined |
| DP with a table | O(n) here, O(n·k) generally | **O(n)** | One stored answer per subproblem |
| DP with rolling variables | O(n) | **O(1)** | Only the last row is ever read |
| **Greedy** | **O(n)** | **O(1)** | One pass, a fixed number of running variables |

The row that matters is the last two. **A 1-D DP whose recurrence only reaches
back one step collapses into O(1) space — and at that point it is
indistinguishable from a greedy scan.** That is not a coincidence; see
[[../concepts/kadane|Kadane]] below. The two families meet exactly there.

Sorting-based greedies are the other common shape, and they cost **O(n log n)**
— the sort dominates the linear pass that follows. If you sort, say so; it is
the whole complexity.

---

## 5. The patterns

One per problem-shape in this topic. Learn the trigger, not the code.

### Pattern A — the running sum that resets → [[../concepts/kadane|Kadane]]
> **Trigger:** "contiguous subarray", "largest sum", "best window", and the array
> contains negatives

Contiguity is the tell. A **contiguous** block means the only two decisions at
each element are *extend the block I'm in* or *start a fresh block here*. That
is a binary local choice with no lookahead — the greedy shape.

The rule that makes it work: a running prefix that has gone **negative is a
liability, not an asset.** Whatever comes next is strictly better off without
it. So when the running sum turns against you, drop it and restart from the
current element.

Two variables carry the whole computation: the sum of the block you're currently
in, and the best block seen anywhere so far. **Keep them separate.** Conflating
them is the single most common bug in this pattern (see Traps).

Here is the mechanism traced over an array with negatives. `cur` is the block
ending at this index; `best` is the answer so far.

```
nums:     -2    1    -3    4    -1     2     1    -5     4

i = 0    x = -2
         cur = -2                      (nothing to extend; start here)
         best = -2

i = 1    x =  1     cur so far = -2  →  negative, a liability
         cur = 1                       ← RESET. drop the -2, start fresh
         best = 1

i = 2    x = -3     cur so far =  1  →  positive, worth keeping
         cur = 1 + (-3) = -2           ← extend
         best = 1                       (unchanged; -2 is not better)

i = 3    x =  4     cur so far = -2  →  liability
         cur = 4                       ← RESET
         best = 4

i = 4    x = -1     cur =  4  →  keep
         cur = 3                       ← extend
         best = 4

i = 5    x =  2     cur =  3  →  keep
         cur = 5                       ← extend
         best = 5                       ← new best

i = 6    x =  1     cur =  5  →  keep
         cur = 6                       ← extend
         best = 6                       ← new best

i = 7    x = -5     cur =  6  →  keep
         cur = 1                       ← extend (still positive! don't reset)
         best = 6

i = 8    x =  4     cur =  1  →  keep
         cur = 5                       ← extend
         best = 6                       (unchanged — 5 < 6)

answer: 6      the block [4, -1, 2, 1]
```

Read `i = 7` twice. `cur` drops hard but stays positive, so the block survives —
and it is correct that it survives, because a positive carry still helps
whatever follows. The reset rule is about the *sign of the accumulated sum*, not
about whether the current element is negative. Getting that distinction wrong is
how people "know Kadane" and still fail the problem.

Also read `i = 8`: `cur` ends at 5 and the answer is 6. **The best block does
not have to be the block you end on.** That's why `best` exists.

### Pattern B — the furthest reachable frontier → [[../concepts/reachability-frontier|Reachability Frontier]]
> **Trigger:** "can you reach", "minimum jumps", "is it possible to get to the
> end", where each position grants a *range* of next positions

The brute-force instinct is a search: from each index, branch to every index it
can reach, recurse. That's exponential, and memoising it gives an O(n²) DP.

The greedy collapse: **you don't care *which* path got you somewhere, only
*how far* anything can get.** So carry a single number — the furthest index
reachable from anything seen so far — and sweep left to right, widening it.

```
index:     0    1    2    3    4    5
values:  [ 2,   3,   1,   1,   4 ]        (each = how far you may step)

i=0  reach = max(reach, 0 + 2) = 2     ░░░▓▓▓
i=1  reach = max(2,     1 + 3) = 4     ░░░▓▓▓▓▓▓
i=2  reach = max(4,     2 + 1) = 4
...

the frontier only ever moves right.  the question is whether an index
you are standing on has already fallen BEHIND the frontier.
```

The whole problem becomes one comparison per index: *is this index still within
the frontier?* The moment it isn't, nothing further is reachable and you're
done. One pass, one variable.

The transferable half is the reduction: **a question about paths became a
question about a single boundary.** Whenever a problem asks "is X reachable"
rather than "what path reaches X", look for a frontier you can carry instead of
paths you'd have to enumerate.

### Pattern C — sort, then sweep
> **Trigger:** "pair these up optimally", "fit as many as possible", "minimum
> number of groups"

Not in this topic's two problems, but it's the third greedy shape and it is
everywhere in [[../curriculum/intervals|Topic 16 — Intervals]], which is next.
Worth naming now: sort the input by the key that makes the local rule obvious,
then take a single pass making the obvious choice. The *whole* design effort
goes into choosing the sort key. Section 8 works an example of this shape.

---

## 6. Python notes

Greedy code is short. What matters is the boilerplate around it being right.

```python
max(x, cur + x)               # "reset or extend", as one expression
cur = best = nums[0]          # seed from a real element, NOT from 0
```

Two idioms, and they carry most of the weight:

- **`max(x, cur + x)`** is the reset-or-extend decision written without an `if`.
  It reads as "is the carried sum helping?" — if `cur` is negative, `x` wins and
  the block restarts. Assembling this into a working scan is your job in the box;
  the point here is that the decision is one expression, not a branch tree.
- **Seeding from `nums[0]`, not `0`.** Seeding `best = 0` silently assumes the
  answer can't be negative. On an all-negative array the correct answer is the
  largest single element, and a zero seed returns 0 — a wrong answer that passes
  every test case you'd write by hand. This is the classic Kadane bug.

Other useful pieces:

```python
float("-inf")                 # safe sentinel when you must seed before the loop
reach = 0                        # a frontier is just an int
reach = max(reach, i + nums[i])  # widening it is one line
items.sort(key=lambda t: t[1])   # sort by the key that makes the rule obvious
```

`max()` with two args is O(1) and reads better than an `if`. `max()` over a
*slice* is O(n) — writing `max(nums[i:])` inside a loop is an accidental O(n²).

---

## 7. Traps

- **Assuming greedy works because it passed the examples.** The given examples
  are small and friendly. Coin change with `{1,3,4}` passes for amounts 1–5 and
  fails at 6. *Always try to construct the counterexample yourself.*
- **Seeding the running best at 0.** Breaks on all-negative input. If the
  problem allows negatives, seed from the first element or `float("-inf")`.
- **Collapsing `cur` and `best` into one variable.** They answer different
  questions — "the block I'm in" versus "the best block anywhere". A single
  variable gets it right on arrays that end on their best block and wrong on
  everything else, which makes the bug survive casual testing.
- **Resetting on a negative *element* instead of a negative *running sum*.**
  See `i = 7` in the trace. `[4, -1, 2, 1]` beats `[4]`.
- **Forgetting to update `best` after the final element**, or updating it before
  `cur`. Order inside the loop matters.
- **Treating the frontier as "where I am".** It's the furthest reachable point
  from anything seen so far — it can be far ahead of the current index, and it
  never moves backwards.
- **Claiming O(1) space and then sorting.** Most sorts are not in-place in the
  sense you mean, and `sorted()` is O(n) extra space. Also: if you sorted, your
  time is O(n log n), not O(n). State it.
- **Saying "greedy" without saying why.** "I'll take the best local option" is a
  description, not a justification. One sentence naming the claim — *"extending
  a negative prefix can never help, so dropping it is safe"* — is the difference
  between a pass and a follow-up you can't answer.
- **Reaching for greedy on a knapsack-shaped problem.** A budget being divided
  among items with different value-per-cost is the canonical greedy failure.
  That shape is DP.

---

## 8. Worked example

Nothing from the problem set, so nothing is spoiled. This one is Pattern C —
sort, then sweep — which the two topic problems don't cover.

> **Problem.** You have cookies of various sizes and children with various
> appetites. A child is content if given a cookie at least as large as their
> appetite. Each child gets at most one cookie. Maximise the number of content
> children.
> `appetites = [1, 2, 3]`, `cookies = [1, 1]` → `1`.

**Brute force.** Try every assignment of cookies to children. That's
permutations — factorial. Say this out loud, then improve on it.

**Form the greedy claim.** The rule that suggests itself: *satisfy the
least-demanding child first, using the smallest cookie that will do.*

**Now attack it.** Can I construct a case where satisfying the hungriest child
first does better? Giving a big cookie to a big appetite consumes a cookie that
could have satisfied a small appetite — and satisfying the small appetite
instead costs a cookie that the big child couldn't have used anyway. Every swap
I try either keeps the count the same or lowers it. The claim holds up. (The
exchange argument: if an optimal solution gives child `c` a cookie larger than
the smallest sufficient one, swapping in the smaller cookie leaves the count
unchanged and frees a bigger cookie. So a greedy start is safe.)

**Implement.**

```python
def content_children(appetites, cookies):
    appetites.sort()
    cookies.sort()
    child = cookie = 0
    while child < len(appetites) and cookie < len(cookies):
        if cookies[cookie] >= appetites[child]:
            child += 1          # content — move to the next child
        cookie += 1             # this cookie is spent either way
    return child
```

**Complexity.** O(n log n + m log m) — the sorts dominate; the sweep is O(n + m).
O(1) extra space beyond the sort.

**The move to internalise:** the algorithm is six lines and the sort is the
whole idea. Almost all the work was (a) choosing what to sort by and (b)
*trying to break the claim before trusting it*. Do both in that order, every
time. In an interview, narrate both — the attack is the part that earns trust.

---

## 9. Materials

Full list on [[../meta/resources|Resources]]. In order:

1. [Greedy algorithms](https://www.geeksforgeeks.org/greedy-algorithms/) — 15 min.
   Skim the intro, read the section on when greedy fails carefully.
2. [Largest sum contiguous subarray](https://www.geeksforgeeks.org/largest-sum-contiguous-subarray/)
   — Kadane, worked slowly. **Read this after your timed attempt, not before.**
3. [Introduction to the exchange argument](https://www.geeksforgeeks.org/introduction-to-exchange-argument/)
   — the proof shape from section 3. Optional, but it's what "prove it's greedy-safe"
   actually means.
4. [NeetCode on YouTube](https://www.youtube.com/@NeetCode) — search the problem
   name, **only after** a timed attempt and a debrief.
5. For extra reps on the sort-then-sweep shape, search LeetCode for *Assign
   Cookies*, *Gas Station*, and *Task Scheduler* — none are in the Blind 75, so
   they cost you nothing.

---

## 10. Self-check

Answer these **aloud**, in full sentences, before starting the timer.

1. In one sentence each: what is the greedy-choice property, and what is optimal
   substructure? Which one does DP *not* need?
2. Give the coin-change counterexample from memory — the denominations, the
   amount, and both answers.
3. Why is "greedy works for coin change" a statement about the coins rather than
   about the problem?
4. What is the practical thirty-second move to make before committing to a
   greedy solution in an interview?
5. In Kadane, what exactly triggers a reset — and why is it *not* "the current
   element is negative"?
6. Why do `cur` and `best` have to be separate variables? Give an array where
   merging them produces a wrong answer.
7. What goes wrong if you seed the running best at `0`?
8. What does the reachability frontier represent, and in what direction can it
   move?
9. Name a problem shape where greedy is almost always wrong, and say what to use
   instead.
10. Give the trigger phrase for each of patterns A, B, and C.

---

## 11. Ready?

**First timed problem: Maximum Subarray** (Medium) — Pattern A.

Say `timed maximum-subarray` and I'll give you the statement and start the
30-minute box. No hints while it's running.

**What "good" looks like here:**

- You state the O(n²) brute force — every subarray, sum it — and its complexity,
  *before* writing anything fast
- You notice that the array contains negatives and say why that matters
- You name the local decision explicitly: extend, or restart here
- You justify the restart rule in one sentence, rather than just coding it
- `cur` and `best` are separate, and both are seeded from a real element
- You test `[-3]`, `[-2,-1,-5]`, and `[1,2,3]` before saying done
- You state O(n) time, O(1) space, unprompted

If you finish inside 30 minutes, don't stop the clock — roll into **Jump Game**
and use what's left. Both problems are one-pass, one-or-two-variable scans; if
the first one clicks, the second usually follows in the same session.

If it doesn't finish, that's expected and it's data. It goes in the
[[../meta/review-queue|review queue]] at +2 days. **The plan is to repeat until
it's cold-solvable, not to clear the list once.**
