---
type: problem
updated: 2026-09-16
sources: [https://leetcode.com/problems/top-k-frequent-elements/]
tags: [arrays-hashing, medium, bucket-sort]
---

# Top K Frequent Elements

**Source:** [LeetCode 347](https://leetcode.com/problems/top-k-frequent-elements/) ·
**Difficulty:** Medium · **Date:** 2026-09-16 · **Time taken:** 18 min 41 s of a
30 min box

Topic 1, problem 5 of [[../curriculum/arrays-hashing|Arrays & Hashing]]. Second
Medium, same evening as [[group-anagrams|Group Anagrams]].

## Statement

Return the `k` most frequent elements of `nums`, any order.
`1 <= len(nums) <= 10^5`, values in `-10^4..10^4`, `k` within the unique count.
**Follow-up in the statement: must beat O(n log n)** — which rules out sorting by
frequency, the obvious first answer.

Also in the statement, and load-bearing: **"it is guaranteed that the answer is
unique."** See below.

## Result

`solved cold` — unaided, correct, follow-up bound cleared.

## Concepts used

[[../concepts/frequency-map|Frequency Map]] · [[../concepts/bucket-sort|Bucket Sort]]
(arrived at in an unusual form) ·
[[../concepts/constraint-bounded-complexity|Constraint-Bounded Complexity]]

## My approach

```python
class Solution:
    def topKFrequent(self, nums: list[int], k: int) -> list[int]:
        count = dict()
        frequency = [None]*len(nums)

        for num in nums:
            count[num] = count.get(num, 0) + 1
            pos = count[num] - 1
            if frequency[pos] is None:
                frequency[pos] = []
            frequency[pos].append(num)

        for item in reversed(frequency):
            if item is not None and len(item) == k:
                return item

        return []
```

**This is not textbook bucket sort, and the difference is the interesting part.**

Standard bucket sort counts first, *then* places each number in the bucket for
its final count, then walks down collecting until it has `k`.

This appends on **every increment** and never removes. So `frequency[i]` ends up
holding **every number occurring at least `i+1` times** — a cumulative "at least"
list, not an "exactly" bucket. The lists grow monotonically as `i` decreases, so
scanning from the top down, the first list of length exactly `k` *is* the top-k
set.

Arrived at cold, in the box, with no wrong turns.

## The guarantee it rests on

**Correct because the answer is guaranteed unique — and that was not said aloud.**

Let counts descending be `c₁ ≥ c₂ ≥ … ≥ cₘ`. Uniqueness means `c_k > c_{k+1}`.
At `i = c_k − 1` the list holds exactly the top `k`. For any `i` above that, the
list holds fewer than `k`, so no false match can occur on the way down. The
target length is therefore hit exactly, never skipped.

Remove the guarantee and the length **jumps past** `k` without equalling it, and
the function falls through to `return []`:

```
nums = [4,5,2], k = 1  →  returns []   (correct answer: any one of them)
```

**Verified by fuzzing, 2026-09-16:**

| Input class | Tested | Mismatches vs `Counter.most_common` |
|---|---|---|
| Valid (answer unique) | 40,368 | **0** |
| Ambiguous (ties at the boundary) | 19,558 | **19,558** |

Not an edge case — it is *every* tie. Leaning on the constraint is legitimate;
**not naming it is the risk.** The sentence to have ready:

> "This relies on the answer being unique. With ties at the boundary the bucket
> length skips past k — I'd switch to collecting from the top until I have k."

## The expected solution

Standard bucket sort — count **first**, then bucket by final count:

```python
from collections import Counter

class Solution:
    def topKFrequent(self, nums: list[int], k: int) -> list[int]:
        count = Counter(nums)
        buckets = [[] for _ in range(len(nums) + 1)]
        for num, freq in count.items():
            buckets[freq].append(num)

        result = []
        for freq in range(len(buckets) - 1, 0, -1):
            for num in buckets[freq]:
                result.append(num)
                if len(result) == k:
                    return result
```

| | Mine | Standard |
|---|---|---|
| Bucket contents | "at least `f` times" — cumulative | "exactly `f` times" |
| Built | during the count pass | after it |
| Extraction | first bucket with `len == k` | accumulate until `len(result) == k` |
| Needs the uniqueness guarantee? | **yes** | no |

**The accumulate-until-`k` loop is what buys the robustness.** It takes elements
one at a time, so it stops at exactly `k` however a tied bucket is laid out —
where a length check has to *land* on `k` and can skip past it. Verified
2026-09-16: the standard form is correct on **19,619 / 19,619** tie inputs, the
same class where mine returns `[]`.

Both are O(n). Mine does less work extracting; it is narrower, not worse.

### The heap answer, worth naming

```python
return [num for num, _ in Counter(nums).most_common(k)]
```

`most_common` uses a heap — **O(n log k)**. Fine to write, but say what it does
underneath or it reads as dodging the question.

**Why bucket sort is the expected answer:** the follow-up demands better than
O(n log n), and O(n log k) technically satisfies that while O(n) is the real
target. Bucket sort gets there because **frequencies are bounded by `n`, so they
can be array indices rather than things to compare** — which is why it escapes
the comparison-sort lower bound. It is not a comparison sort.

The strong rejection sentence for this problem is therefore the heap, not
sorting: *"A heap gives O(n log k). But frequencies are bounded by n, so I can
index by them instead of comparing — bucket sort, O(n)."*

## Complexity

**Time O(n). Space O(n).** Stated correctly in the box.

One append per iteration of `nums`, so building the lists is O(n) in total, not
O(n) per element — the point worth being explicit about, since the nested-looking
structure invites an O(n²) guess. Second pass is O(n). **Follow-up bound cleared.**

**The half that was wrong:** *"space O(n) … to account for worst case of each
number being unique."* They cannot all be unique. Values run `-10⁴..10⁴`, so the
count dict caps at **20,001 entries** however large `n` gets. The dict is
bounded; the `frequency` list is what is genuinely O(n). Right total, wrong
reason for half of it — and it is the same bounded-key-space fact that was
handled correctly on [[valid-anagram|Valid Anagram]].

## Drills

| Drill | Result |
|---|---|
| D1 — name what you rejected | **pass, 2 of 2 → retires.** "Sorting, because that would be O(n log n)" — approach *and* reason |
| D3 — name the stdlib tool | **pass, 1 of 2.** First fire. *"I could have used `Counter` but chose not to"* — names it, then chooses deliberately |
| D4 — space against the constraints | **miss.** Claimed an all-unique worst case the value range forbids. Stays live |

## Code quality

`count.get(num, 0) + 1` — the shape that took three problems to arrive at, now
automatic. Naming is fine throughout.

`return []` is unreachable under the guarantee. A one-line comment saying so
would demonstrate that the dependency was noticed.

## Fixes before the re-attempt

1. **Name the guarantee you are leaning on.** Any time a constraint makes the
   solution work, say which one and what breaks without it.
2. **Check "worst case" claims against the value range**, not just against `n`.
3. State that the appends total O(n) across the whole loop — pre-empt the O(n²)
   guess.

## Re-attempt

Scheduled in [[../meta/review-queue|review queue]] for **2026-09-19** (+3d).
Pass condition: solved cold, and the uniqueness guarantee named unprompted with
what breaks without it.
