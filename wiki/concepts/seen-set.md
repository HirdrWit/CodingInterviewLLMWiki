---
type: concept
status: solid
updated: 2026-09-13
sources: []
tags: [arrays-hashing]
---

# Seen-Set

**In one sentence:** Walk the input once carrying a set of everything already
passed, and ask that set before processing each element — turning a nested
"have I encountered this?" scan from O(n²) into O(n).

Pattern A in [[../lessons/01-arrays-hashing|Lesson 1]]. The plainest possible
instance of the trade the whole topic teaches: **spend memory, buy time.**

## When to reach for it

Trigger: "does it contain a duplicate", "is every element unique", "have we
visited this node already".

Generally: any time the inner loop of a brute force exists **only to search**.
That inner loop is not computing anything — it is answering a membership
question, and membership questions are what sets are for.

## How it works

```python
seen = set()
for x in items:
    if x in seen:
        return True        # or: handle the repeat
    seen.add(x)
return False
```

Two details carry the weight:

- **Check before adding.** Reversing them makes every element match itself.
- **Return early.** The answer is known the instant a repeat appears; there is
  nothing to gain by finishing the scan.

## Set or map?

Set when you care *whether* you have seen something. Map when you care *what
you saw with it*.

The tell that you picked wrong: **a dict whose values you never read.** If you
had to invent a placeholder (`seen[x] = 1`, `seen[x] = True`) to satisfy the
data structure, the data structure is a set. Same machine, same O(1) average
lookup — but the choice communicates to a reader what you understood the
problem to need.

## Complexity

O(n) time **average**, O(n) space.

Worst case is **O(n²)**: if every key collides into one bucket, each membership
check degrades to scanning up to n entries. Live in Python, where `hash(x) == x`
for ints, so integers spaced by the table size collide by construction.

Best case O(1) time and space — an early repeat exits after two elements.

## Gotchas

- `x in my_list` is O(n). Identical syntax to `x in my_set`, completely
  different cost — the most common way to write an accidental O(n²).
- Unhashable keys: `tuple()` a list before using it as a set member.
- The O(n) space is a real cost. Some interviewers follow up with "now do it in
  O(1) space" — which usually means sorting, or [[two-pointers]].

## Related

- [[hash-map]] — the machine underneath
- [[frequency-map]] — the same scan when you need counts, not just presence
- [[complement-lookup]] — the same scan when you need a *partner*, not a repeat

## Evidence

- [[../problems/contains-duplicate|Contains Duplicate]] — solved cold unaided in
  7 min, 2026-09-13. Correct algorithm first try; used a dict with a dummy
  value where a set belonged. `solid`, not higher: one solve, one shape.
