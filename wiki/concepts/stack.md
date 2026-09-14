---
type: concept
status: unseen
updated: 2026-09-14
sources: [https://www.geeksforgeeks.org/stack-data-structure/, https://docs.python.org/3/tutorial/datastructures.html#using-lists-as-stacks]
tags: [stack]
---

# Stack

**In one sentence** — a pile you can only touch the top of: things come out in
the reverse of the order they went in, which is exactly the order you need
whenever the most recently started thing must finish first.

Introduced in [[../lessons/04-stack|Lesson 4]].

## When to reach for it

The trigger is never the word "stack". It is one of these:

- **Nesting.** Something opens, something closes, and the innermost must close
  first. → [[lifo-matching]]
- **Undo.** Processing element `i` can cancel something already processed.
- **"Most recent unresolved".** You're carrying a pile of things seen but not yet
  answered, and the answer arrives for the newest one first. → [[monotonic-stack]]
- **"Do it without recursion."** Recursion is a stack; make it explicit.

The anti-trigger is just as useful: if you ever want to look at anything other
than the top — index into it, search it, take the *oldest* item — a stack is the
wrong structure. Oldest-first means a queue.

## How it works

Three operations, and that is all of them:

| Operation | Python | Cost |
|---|---|---|
| push | `stack.append(x)` | O(1) amortized |
| pop | `stack.pop()` | O(1) |
| peek | `stack[-1]` | O(1) |
| empty? | `not stack` | O(1) |

A stack is a dynamic array where the "top" is the **end** of the list. That is
the whole implementation — pushing and popping at the end move nothing, so both
are O(1). Pushing or popping at the *front* would shift every element: O(n).
This is why `list.pop(0)` is a bug and `list.pop()` is not.

`append` is O(1) **amortized** rather than plainly O(1): when the underlying
array fills up, it is reallocated at a larger size and everything is copied, an
O(n) event. Because the capacity grows proportionally, those events get rarer as
the list grows and the cost per push averages to a constant.

## Minimal example

```python
stack = []
stack.append(1)        # [1]
stack.append(2)        # [1, 2]
stack[-1]              # 2      — peek, nothing removed
stack.pop()            # 2      — [1]
if not stack:          # False
    ...

# the idiom that prevents most stack bugs:
if stack and stack[-1] == target:
    stack.pop()
```

Python short-circuits `and`, so the emptiness check protects the index. Write it
in that order every time.

## Complexity

O(1) for every legal operation. O(n) space for n items — and that space is a
real cost that has to be stated: a stack-based solution is **not** O(1) space,
however tidy it looks.

For a recursive solution, the call stack counts too: depth d costs **O(d)
space** even when the function body allocates nothing.

## Gotchas

- `pop()` on an empty list raises `IndexError`. Guard before every pop and every
  `stack[-1]`.
- `pop(0)` is O(n). If FIFO is what's wanted, use `collections.deque` and
  `popleft()`.
- **A stack reverses order.** When pushing several items you want processed in a
  given order, push them in reverse.
- Don't write a `Stack` class in an interview. A `list` is the stack.
- CPython's recursion limit is ~1000 frames. Raising it is almost never the
  right fix; converting to an explicit stack is.

## Related

- [[lifo-matching]] — the commonest problem shape a stack solves
- [[monotonic-stack]] — a stack with an added ordering invariant, which turns
  "next greater element" from O(n²) into O(n)
- [[hash-map]] — different trade entirely: the hash map buys O(1) *search* at
  the cost of order, the stack buys O(1) *ordering discipline* and gives up
  search
- [[two-pointers]] — the O(1)-space alternative when the problem's state is
  bounded; the stack is what you reach for when it isn't

**Stack vs queue:** LIFO vs FIFO. Same loop, different structure — DFS uses a
stack, BFS uses a queue. That fork recurs from Topic 7 onward.

## Evidence

Problems where I used this unaided:

*(none yet — [[../curriculum/stack|Topic 4]] not started)*
