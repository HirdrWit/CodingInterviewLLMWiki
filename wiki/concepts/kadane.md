---
type: concept
status: unseen
updated: 2026-09-14
sources: [https://www.geeksforgeeks.org/largest-sum-contiguous-subarray/]
tags: [greedy, dynamic-programming, arrays]
---

# Kadane's Scan

**In one sentence, in my own words** — one left-to-right pass carrying two
numbers, the sum of the block I'm currently in and the best block I've seen
anywhere, where the only decision at each element is *extend the block* or
*throw the prefix away and start here* — and you throw it away the moment the
carried sum has gone negative.

Pattern A in [[../lessons/15-greedy|Lesson 15 — Greedy]].

## When to reach for it

The trigger is **"contiguous"** plus **"largest/smallest sum"** plus the array
**contains negatives**. All three matter:

- *Contiguous* is the real tell. A contiguous block means there are exactly two
  local options at each element — extend, or restart — which is a binary choice
  with no lookahead. That's the greedy shape.
- If there were no negatives the answer would be the whole array, and there'd be
  no problem.

Softer variants that are the same machine: "best window", "maximum product
subarray" (same scan, carrying two running values instead of one), "best day to
buy and sell".

If the problem asks you to return the *indices* of the block rather than its
sum, it's still Kadane — you just record where the last reset happened.

## How it works

The licensing claim, and it is one sentence, so say it out loud:

> **A running prefix whose sum has gone negative is a liability.** Whatever comes
> next is strictly better off without it, so dropping it can never cost you the
> optimum.

That is the [[greedy-choice]] property for this problem, and it is genuinely
true rather than plausible-sounding: if `cur < 0`, then `cur + x < x` for every
`x`, so the block starting fresh at `x` beats the extended one, always.

Two variables, and **they must stay separate**:

| Variable | Answers |
|---|---|
| `cur` | the sum of the block *ending at this index* |
| `best` | the best block seen *anywhere so far* |

The best block does not have to be the block you end on — that's the entire
reason `best` exists.

### Both readings

This is the place the greedy and DP families touch, and Lesson 15 makes that the
point rather than a footnote.

**As greedy:** at each element, take the locally best option — keep the carry if
it helps, discard it if it doesn't — and never revise.

**As [[dynamic-programming|DP]]:** define `dp[i]` = the largest sum of a block
ending exactly at index `i`. The recurrence is

```
dp[i] = max(nums[i], dp[i-1] + nums[i])
answer = max(dp)
```

`dp[i]` reaches back **exactly one step**, so the table collapses to a single
rolling variable and the O(n) space becomes O(1). At that point the DP *is* the
greedy scan, character for character. They are not two algorithms that happen to
agree; they're one algorithm seen from two directions.

Which reading to give an interviewer: lead with the DP recurrence if you want
to show where the answer came from, lead with the greedy sentence if you want to
show why it's safe. Being able to switch on request is the strong answer.

## Minimal example

Traced on `[-2, 1, -3, 4, -1, 2, 1, -5, 4]`:

```
x:      -2     1    -3     4    -1     2     1    -5     4

cur:    -2     1    -2     4     3     5     6     1     5
              ↑           ↑
            RESET       RESET
         (carry -2)   (carry -2)

best:   -2     1     1     4     4     5     6     6     6
                                            ↑
                                    answer 6 = [4, -1, 2, 1]
```

Read the `-5` column. `cur` drops from 6 to 1 but stays **positive**, so the
block survives — correctly, because a positive carry still helps whatever
follows. The reset rule is about the sign of the *accumulated sum*, never about
the sign of the current element.

Read the last column. `cur` ends at 5; the answer is 6. `best` is what remembers
it.

```python
cur = best = nums[0]      # seed from a real element, not from 0
# per element x (from index 1):
#     cur  = max(x, cur + x)
#     best = max(best, cur)
```

`max(x, cur + x)` is the reset-or-extend decision written as one expression
rather than a branch — if `cur` is negative, `x` wins and the block restarts.

## Complexity

**O(n) time, O(1) space.** One pass, two ints. No allocation, no second
traversal, no sort.

The O(1) is the payoff over the table-based DP, and it is worth stating
explicitly because it's the thing that distinguishes "I know the recurrence"
from "I know why the recurrence collapses".

## Gotchas

- **Seeding `best = 0`.** Assumes the answer can't be negative. On an
  all-negative array the correct answer is the largest single element, and a
  zero seed returns 0 — a wrong answer that passes every test you'd write by
  hand. The classic Kadane bug. Seed from `nums[0]` or `float("-inf")`.
- **Collapsing `cur` and `best` into one variable.** Right on arrays that end on
  their best block, wrong on everything else — which lets the bug survive casual
  testing.
- **Resetting on a negative element instead of a negative running sum.**
  `[4, -1, 2, 1]` beats `[4]`.
- **Order inside the loop.** Update `cur` first, then `best` from the new `cur`.
  The other order is off by one element.
- **Empty input.** `nums[0]` crashes on `[]`. Ask whether it's possible.
- Test `[-3]`, `[-2,-1,-5]`, and `[1,2,3]` before saying done. Those three cover
  the seed bug, the all-negative case, and the never-resets case.

## Related

- [[greedy-choice]] — the property that licenses the discard; this page is its
  cleanest true instance, where coin change is its cleanest failure
- [[dynamic-programming]] — the other reading of the same scan; differs in that
  DP stores a table it doesn't need here
- [[sliding-window]] — also a one-pass contiguous scan, but the window has two
  moving ends and shrinks from the left; Kadane's block only ever restarts
  wholesale
- [[reachability-frontier]] — the other one-variable greedy scan in Lesson 15;
  carries a boundary rather than a sum

## Evidence

Problems where I used this unaided:
