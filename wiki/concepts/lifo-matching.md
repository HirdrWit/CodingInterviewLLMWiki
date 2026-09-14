---
type: concept
status: unseen
updated: 2026-09-14
sources: []
tags: [stack]
---

# LIFO Matching

**In one sentence** — when tokens come in open/close pairs and the innermost
open must close first, push every open onto a stack and let each close consume
the top; the input is well-formed exactly when every close found its partner and
nothing is left over.

Pattern A in [[../lessons/04-stack|Lesson 4]].

## When to reach for it

Trigger: "valid", "balanced", "properly nested", "well-formed", "matching" —
applied to anything with opening and closing symbols.

The deeper tell is **nesting**, not brackets. If the problem describes things
that can contain other things, and the contained thing must be finished before
the container, the required order is LIFO. Nested tags, nested scopes, nested
expressions, nested function calls — all the same pile. Brackets are just the
smallest possible instance.

The anti-tell: if there is only **one kind** of pair, you don't need a stack at
all — a counter does it in O(1) space, incrementing on open and decrementing on
close, invalid if it ever goes negative or ends non-zero. A stack is only
necessary when you must remember *which* kind of thing is open.

## How it works

Walk the input once.

- **Open token** → push it.
- **Close token** → if the stack is empty, or its top isn't the matching open,
  the input is invalid. Otherwise pop.
- **End of input** → valid only if the stack is empty.

```
"([{}])"

(  push          [ (        ]
[  push          [ (, [     ]
{  push          [ (, [, {  ]
}  top is {  ✓   [ (, [     ]
]  top is [  ✓   [ (        ]
)  top is (  ✓   [          ]
end, empty → valid
```

## Minimal example

```python
pairs = {')': '(', ']': '[', '}': '{'}   # close → open

stack = []
for ch in s:
    if ch in pairs:                       # a closing token
        if not stack or stack.pop() != pairs[ch]:
            return False
        continue
    stack.append(ch)                      # an opening token
return not stack
```

The matching table as a dict rather than a chain of `if`/`elif` is the detail an
interviewer notices — it scales to any number of pair kinds without touching the
loop.

## Complexity

O(n) time: one pass, O(1) work per character. O(n) space: worst case `"((((("`
pushes everything.

You cannot do this in O(1) space in general, and that is worth being able to
argue: with k kinds of bracket you genuinely have to remember the *sequence* of
what is open, and that sequence can be as long as the input.

## Gotchas

- **Two distinct failure modes, caught in two different places.** A bad close is
  caught *inside* the loop; leftovers are caught *after* it. Returning `True`
  instead of `not stack` at the end is the classic way to fail this — `"((("`
  sails through the loop without a single mismatch.
- **Popping an empty stack.** A close with nothing open is not an exception to
  handle defensively, it *is* the invalid case. Check `not stack` first.
- **Order-blind counting.** Counting opens and closes is not enough: `"([)]"`
  has balanced counts and is invalid. The stack is what encodes the order.
- Don't forget non-bracket characters if the problem allows them — decide
  whether they're ignored or illegal, and say which.

## Related

- [[stack]] — the structure this pattern is built on
- [[monotonic-stack]] — the other major stack pattern; there the *pop* produces
  the answer, here the pop merely confirms a match
- [[seen-set]] — the contrast worth holding: a set answers "have I seen this at
  all", a stack answers "what is the most recent unresolved one". Membership vs
  order.

## Evidence

Problems where I used this unaided:

*(none yet — Valid Parentheses in [[../curriculum/stack|Topic 4]] not attempted)*
