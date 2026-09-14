---
type: concept
status: unseen
updated: 2026-09-14
sources: [https://docs.python.org/3/howto/sorting.html]
tags: [two-pointers, algorithms]
---

# Sort, Then Scan

**In one sentence, in my own words** — pay O(n log n) up front to put the input
in order, because order is the structure that makes every later step a cheap
linear pass instead of a search.

Pattern B in [[../lessons/02-two-pointers|Lesson 2]]. Sorting here is not the
answer; it's the **preprocessing that creates the invariant** the real answer
needs.

## When to reach for it

Trigger: the problem is about **values, not positions**, the input is unsorted,
and the thing you want to do next would be easy on sorted data — find pairs,
find triples, detect near-duplicates, group by closeness, merge ranges.

The deciding question is always the same: *what does sortedness buy me here?*
If the answer is "a monotonic quantity I can steer two pointers with", sort. If
you can't say what it buys, sorting is a reflex, not a decision.

## The checklist before you sort

Three questions, in order. Any "no" kills it:

1. **Does the problem need original indices?** Sorting destroys them. This is
   the entire reason Two Sum (returns indices) is a hash-map problem while Three
   Sum (returns values) is a sorting problem. **Read the return type first.** If
   you genuinely need both, sort `(value, index)` tuples and carry the index
   along — but that costs O(n) space and usually means a hash map was the better
   call.
2. **Does the problem care about the original order?** Subarray and substring
   problems do, by definition — the elements must stay contiguous in their
   original arrangement. Sorting is illegal there, not merely unhelpful.
3. **Will the sort be dominated anyway?** If what follows is O(n²), the
   O(n log n) sort vanishes from the bound and is effectively free. Take it
   without hesitating.

## How it works

```python
nums.sort()          # in place: O(1) extra space, keeps an O(1)-space claim honest
# ordered = sorted(nums)   # new list: O(n) space. Fine — but say so out loud.

lo, hi = 0, len(nums) - 1
while lo < hi:
    ...              # now every comparison carries "everything left is smaller"
```

The invariant the sort bought: **`nums[i] <= nums[j]` whenever `i < j`.** Every
subsequent trick — converging pointers, binary search, "duplicates are adjacent"
— is a consequence of that one line.

That last consequence is worth naming on its own: after sorting, **equal
elements are neighbours**. Duplicate detection and
[[duplicate-skipping|duplicate skipping]] become `if nums[i] == nums[i-1]`, a
local check instead of a global one.

## Complexity

O(n log n) for the sort — the comparison-sort floor, so this is not improvable
without extra assumptions (counting sort needs a bounded key range; see
[[constraint-bounded-complexity]]).

Space: `list.sort()` is in place; `sorted()` allocates a new list, O(n). CPython
uses Timsort either way, which needs O(n) auxiliary space in the worst case even
in place — so the strictly-correct phrasing for a converging scan afterwards is
**"O(1) space beyond the sort."**

The total is `O(n log n) + O(whatever follows)`. Read which term wins:

| What follows | Total |
|---|---|
| One linear scan | O(n log n) — the sort dominates |
| Converging two pointers | O(n log n) — the sort dominates |
| Fix-one + converging pair (triples) | **O(n²)** — the sort disappears |

## Gotchas

*This section grows every time I get it wrong. That's its job.*

- Sorting when the answer is indices. The most expensive version of this mistake
  is not noticing until the test output is wrong.
- Claiming O(1) space after `sorted()`. Use `.sort()` or adjust the claim.
- Sorting a subarray/substring problem. Order *is* the problem there.
- Forgetting the sort exists when stating complexity — "it's O(n), two pointers"
  is wrong if you sorted first and nothing else is quadratic.
- Sorting input that arrived sorted. If the statement says "sorted", the
  O(n log n) is pure waste and skipping it is part of the expected answer.
- Reaching for a sort when a hash map is O(n). Sorting is the *space*-cheap
  option, not always the *time*-cheap one. Know which resource the follow-up
  question is actually about.

## Related

- [[opposite-end-pointers]] — the usual consumer of the order this creates.
- [[duplicate-skipping]] — only cheap *because* sorting made equal elements
  adjacent.
- [[complement-lookup]] — the alternative that skips sorting entirely: O(n) time,
  O(n) space, works unsorted, preserves indices. The direct trade-off.
- [[constraint-bounded-complexity]] — when the constraints bound the key range, a
  non-comparison sort beats the O(n log n) floor.
- [[../skills/algorithms|Algorithms]] — parent skill.

## Evidence

Problems where I used this unaided:

*None yet. Add links here — and only then promote the status.*
