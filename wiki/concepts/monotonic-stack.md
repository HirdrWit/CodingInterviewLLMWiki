---
type: concept
status: unseen
updated: 2026-09-14
sources: [https://www.geeksforgeeks.org/next-greater-element/]
tags: [stack]
---

# Monotonic Stack

**In one sentence** — a stack kept permanently sorted by popping anything that
would break the order before pushing, so that each pop announces the answer for
the element being popped: "the thing that just arrived is your next greater
element."

Pattern C in [[../lessons/04-stack|Lesson 4]].

**Why it's in the wiki with no Blind 75 problem behind it.** Topic 4 contributes
exactly one problem, Valid Parentheses, and it doesn't use this. But the
monotonic stack is the standard follow-up to finishing that problem early, and
it is the technique behind Daily Temperatures, Next Greater Element, Largest
Rectangle in Histogram and the stack solution to Trapping Rain Water. It gets
learned here, deliberately, out of band.

## When to reach for it

Trigger: "**next** greater / smaller element", "**previous** greater / smaller",
"how many days until a warmer one", "the nearest bar taller than this one",
"span", "largest rectangle".

The generalised shape: **for every element, find the nearest element in some
direction satisfying a comparison.** The brute force is a nested loop scanning
outward from each position — O(n²). Seeing that nested loop, where the inner
scan is looking for the *first* element that beats the outer one, is the tell.

## How it works

Keep a stack of **indices** whose values are monotonically ordered — decreasing
for "next greater", increasing for "next smaller". Before pushing, pop
everything that violates the order.

```
heights [2, 1, 5, 6], next greater element for each
stack holds indices; values are kept DECREASING

see 2 → empty, push                       [2]
see 1 → 1 < 2, order holds, push          [2, 1]
see 5 → 5 > 1 → pop: answer for 1 is 5    [2]
        5 > 2 → pop: answer for 2 is 5    []
        push                              [5]
see 6 → 6 > 5 → pop: answer for 5 is 6    []
        push                              [6]
end   → 6 never popped: no next greater
```

**The pop is the answer, not cleanup.** That is the sentence to remember. An
element sits on the stack precisely because nothing yet seen beats it; the
moment something does, that something is its answer, and it leaves.

## Minimal example

```python
def next_greater(nums):
    result = [-1] * len(nums)
    stack = []                                  # indices, values decreasing
    for i, x in enumerate(nums):
        while stack and nums[stack[-1]] < x:
            result[stack.pop()] = x             # the pop IS the answer
            # for a distance question: i - popped_index
        stack.append(i)
    return result                               # leftovers keep their -1
```

Store **indices**, not values. Most versions of this question ask "how far
away", which needs positions — and the value is always recoverable from the
index, never the reverse.

## Complexity

**O(n) time**, despite the nested `while`. Each index is pushed exactly once and
popped at most once, so the total number of `while` iterations across the whole
run is bounded by n. That amortization argument is the thing to say out loud; it
is what distinguishes understanding this from having memorised it.

O(n) space for the stack.

## Gotchas

- **Looks O(n²), is O(n).** If you can't articulate why, you'll doubt the
  solution mid-interview. Push-once, pop-once.
- **Pick the direction deliberately.** *Next* greater → scan left to right.
  *Previous* greater → scan right to left, or read the stack differently. Decide
  before typing, not during.
- **Strict vs non-strict comparison.** `<` and `<=` in the `while` give
  different answers when values repeat. The problem statement decides it; read
  it twice.
- **Leftovers are meaningful.** Whatever remains on the stack at the end has no
  answer in that direction — the sentinel value (`-1`, `0`) is part of the spec,
  not an oversight.
- Storing values when the question needs distances. Store indices.

## Related

- [[stack]] — the base structure; this adds an ordering invariant to it
- [[lifo-matching]] — the other stack pattern. There the stack is a passive
  record of unresolved work; here it is actively maintained in sorted order, and
  the popping does the computing.
- [[two-pointers]] — the other answer to "collapse a nested scan into one pass".
  Two pointers works when the input is sorted; the monotonic stack works when it
  isn't but the *comparison structure* still lets you discard candidates.

## Evidence

Problems where I used this unaided:

*(none yet — the Daily Temperatures exercise at the end of
[[../lessons/04-stack|Lesson 4]] is where the first evidence comes from)*
