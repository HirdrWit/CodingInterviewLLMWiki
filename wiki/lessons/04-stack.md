---
type: lesson
topic: stack
status: ready
updated: 2026-09-14
sources: [https://www.geeksforgeeks.org/stack-data-structure/, https://www.geeksforgeeks.org/stack-in-python/, https://docs.python.org/3/tutorial/datastructures.html#using-lists-as-stacks, https://www.geeksforgeeks.org/complexity-cheat-sheet-for-python-operations/]
tags: [lesson, stack]
---

# Lesson 4 — Stack

**Curriculum:** [[../curriculum/stack|Topic 4]] · **Materials:** [[../meta/resources|Resources]]
**Prerequisites:** [[../curriculum/arrays-hashing|Topic 1]] (arrays, amortized append)
**Work through in:** 45–60 minutes.

---

## 1. Why this topic

**One Blind 75 problem lands here.** Valid Parentheses, an Easy. By problem
count this is the smallest topic on the roadmap, and if you judged it by that
you would skim it in ten minutes and move on.

Don't. Here is the honest accounting of why this lesson is as long as it is.

**The obvious reason:** you need a stack to solve Valid Parentheses, and that
takes about fifteen lines.

**The real reason:** the stack is the control-flow primitive that the entire
second half of this curriculum is built on, and it arrives here *undisguised*.
It will not be labelled "stack" again. It comes back as:

- **[[../curriculum/index|Topic 7 — Trees]].** Every iterative tree traversal is
  an explicit stack. "Now do it without recursion" is a standard follow-up, and
  it is a stack question wearing a tree costume.
- **[[../curriculum/index|Topic 9 — Backtracking]] and [[../curriculum/index|Topic 11 — Graphs]].**
  DFS *is* a stack. Recursive DFS uses the machine's stack; iterative DFS uses
  one you allocated yourself. They are the same algorithm, and knowing that is
  what lets you convert between them under pressure.
- **Recursion generally.** Every recursive call you have ever written pushed a
  frame onto a stack. "Why does this blow up at depth 1000?" and "what's the
  space complexity of this recursion?" are both stack questions.

There is also a structural reason this topic sits where it does. Topics 1–3
built data structures defined by their **layout** — an array is contiguous
boxes, a hash map is buckets. The stack is the first one defined by a
**restriction**: it is an array with most of its operations *forbidden*. You may
only touch one end. That sounds like a downgrade, and the entire point of the
topic is that it is not — the restriction is what makes the structure a
*reasoning tool* rather than a container.

And one more link back. [[../curriculum/index|Two Pointers]] and
[[../curriculum/index|Sliding Window]] both solved problems by carrying O(1)
extra state across a single pass. Plenty of problems need more than that: they
need to remember an unbounded number of unfinished things, in the order they
were started. That is the next rung up, and it is exactly what a stack is.

---

## 2. The mental model

*(Concept page: [[../concepts/stack|Stack]].)*

### A stack is a spring-loaded plate dispenser

The one in a cafeteria. Plates go in from the top, come out from the top, and
the spring means you can only ever see and touch the topmost one. The plate at
the bottom went in first and comes out last.

That is the whole definition: **LIFO — last in, first out.**

```
        push("D")                        pop() → "D"
            │                                 ▲
            ▼                                 │
          ┌───┐                             ┌───┐
   top →  │ D │                      top →  │ C │
          ├───┤                             ├───┤
          │ C │                             │ B │
          ├───┤                             ├───┤
          │ B │                             │ A │
          ├───┤                             └───┘
          │ A │  ← bottom, arrived first
          └───┘
```

Three operations, and that is genuinely all of them:

| Operation | Means |
|---|---|
| `push(x)` | put `x` on top |
| `pop()` | remove and return the top |
| `peek()` | look at the top without removing it |

No indexing. No searching. No "give me the third one down". If your solution
wants any of those, a stack is the wrong structure and you should notice that
immediately.

### The better mental model: a pile of unfinished business

The plate picture tells you the mechanics. This one tells you *when to reach
for it*.

**A stack holds things you have seen but not yet resolved, in the order you
started them.** Every time you begin something that can't be finished yet, you
push it. When the thing that finishes it arrives, you pop.

```
input:     (   [   {   }   ]   )
           │   │   │   │   │   │
push  →    (       resolves ────┘
push  →    (  [    resolves ────┘
push  →    (  [  { resolves ─┘
                   ↑
           pile of business opened and not yet closed
```

Read that picture again, because it is the transferable half of this entire
lesson. "Opened and not yet closed" is the shape. It describes brackets, but it
also describes an unfinished function call, an unexplored branch of a tree, a
directory you descended into, and an element that hasn't yet met a bigger
number. Same pile, different contents.

**Why LIFO and not FIFO?** Because nesting. The most recently opened thing must
close *first* — you cannot close the outer bracket while an inner one is still
open. Whenever a problem has that "innermost finishes first" structure, the
order you need is LIFO, and the structure that gives you LIFO is a stack.

A queue (FIFO) is the mirror image — first in, first out — and it answers the
opposite question, "what has been waiting longest". That fork shows up again in
Topic 7 and Topic 11: **DFS uses a stack, BFS uses a queue.** The algorithms are
otherwise nearly identical. The data structure *is* the difference.

---

## 3. The mechanics — enough to reason about cost

### It's an array with two operations

You do not need a `Stack` class. A stack is a dynamic array where you only ever
touch the last slot.

```
list:     [ A │ B │ C │   │   │   ]     capacity 6, length 3
                    ▲
                    └── the "top" is just index -1

append("D") → write into the next free slot. No shifting. O(1).
pop()       → return the last slot, shrink length by one. No shifting. O(1).
```

Compare that with pushing at the **front**: every existing element has to shift
up one, which is O(n). This is why the top of the stack is the *end* of the
list, not the beginning, and why `list.pop(0)` is a silent O(n) that turns an
O(n) algorithm into O(n²).

### Why `append` is O(1) *amortized*, not O(1)

Same machinery as the hash table growth from [[01-arrays-hashing|Lesson 1]].
When the underlying array runs out of capacity, Python allocates a bigger one
and copies everything across — that single append costs O(n). But the capacity
grows *proportionally*, so the expensive copies get rarer as the list gets
bigger, and the cost spread over all n appends works out to a constant each.

Say "O(1) amortized" when asked. It is the same correctness instinct as saying
"O(1) average" about a hash lookup: it tells the interviewer you know where the
claim comes from instead of having memorised it.

### The call stack — recursion *is* a stack

This is the part that pays off in Topics 7, 9 and 11, so do not skim it.

When a function calls another function, the machine has to remember where to
come back to and what the local variables were. It stores that in a **stack
frame** and pushes it onto the **call stack**. When the function returns, its
frame is popped and execution resumes from the saved return address.

```python
def f(n):
    if n == 0:
        return 0
    return n + f(n - 1)

f(3)
```

```
       call stack, at the deepest point
       ┌──────────────────┐
top →  │ f(0)  n=0        │  ← about to return 0
       ├──────────────────┤
       │ f(1)  n=1        │  waiting on f(0)
       ├──────────────────┤
       │ f(2)  n=2        │  waiting on f(1)
       ├──────────────────┤
       │ f(3)  n=3        │  waiting on f(2)
       └──────────────────┘
```

Three consequences you should be able to state out loud:

**1. Recursion is not free space.** That stack is real memory. A recursion of
depth d costs **O(d) space** even if the function body allocates nothing. When
you are asked for the space complexity of a recursive solution, the answer
includes the call stack — people forget this constantly and get corrected in
interviews.

**2. It has a hard limit.** CPython caps recursion depth at roughly 1000 by
default (`sys.getrecursionlimit()`) and raises `RecursionError` past it. This is
not hypothetical: a linked list or a degenerate, one-sided tree of 10⁵ nodes
will blow it. Expect "what happens on a skewed tree?" as a follow-up in Topic 7.

**3. Any recursion can be rewritten with an explicit stack.** If the machine can
do it with a stack of frames, you can do it with a list of the same state. That
is the entire trick behind iterative traversal:

```
recursive                          iterative
---------                          ---------
call f(x)              ≡           stack.append(x)
return                 ≡           stack.pop()
the call stack         ≡           your list
```

The conversion is mechanical once you see it as the same object. **The only real
work is deciding what state each "frame" has to carry.**

---

## 4. Complexity — the table to know cold

| Operation | Cost | Why |
|---|---|---|
| `push` / `append` | **O(1) amortized** | write to the end; occasional resize-and-copy, amortized away |
| `pop` from the top | **O(1)** | read the last slot, shrink the length; nothing shifts |
| `peek` (`stack[-1]`) | **O(1)** | plain index arithmetic |
| `is empty` (`not stack`) | **O(1)** | a length check |
| Search for a value | **O(n)** | and if you need this, you picked the wrong structure |
| Access the k-th element | **O(n)** conceptually | a real stack has no indexing; Python lets you cheat, so don't |
| Space for n items | **O(n)** | and this counts against you — see the traps |

Two entries deserve their own note.

**Search is O(n) and that's fine.** It is not a defect. The stack trades away
random access on purpose; what you get in return is an ordering discipline that
makes certain problems trivial. Complaining that a stack can't search is like
complaining that a hash map can't iterate in sorted order.

**The O(n) space is the price of the topic.** Single-pass stack solutions are
usually O(n) time and O(n) space. That is normally optimal, but always *state*
the space — and be ready for "can you do it in O(1) space?", which for bracket
matching is an interesting one to reason about (you can't, in general, because
you genuinely have to remember which kinds of brackets are open and in what
order).

---

## 5. The patterns

Each one is **a trigger in the problem statement → the tool it should summon**.
Learn the triggers. The code is fifteen lines either way.

### Pattern A — LIFO matching → [[../concepts/lifo-matching|concept page]]
> **Trigger:** "valid", "balanced", "properly nested", "matching", "well-formed",
> anything with pairs of open/close symbols

This is the Blind 75 problem in this topic, so here is the shape and nothing
more.

The structure of the problem is: some tokens **open** something, some tokens
**close** something, and a close is only legal if it matches the most recently
opened thing still outstanding. Push on open, and on close compare against the
top and pop.

The two details that decide whether you get it right are not the pushing — they
are the **two ways it can be invalid**:

1. A close arrives with nothing outstanding, or with the *wrong* thing on top.
2. The input ends with things still outstanding.

Trap 2 is the one people miss. It is checked *after* the loop, not inside it.
More in section 7.

**The generalisation worth carrying:** this is not about brackets. It is about
any grammar where the innermost thing must finish first. Nested tags, nested
scopes, nested function calls — same pile.

### Pattern B — the stack as "undo" / adjacent cancellation
> **Trigger:** "remove adjacent pairs", "simplify", "collapse", "backspace",
> "the last operation can be cancelled"

When processing element `i` may *undo* something you already processed, a stack
holds the committed-so-far result and lets you retract the most recent commit.

The move: instead of building the answer left to right into a list you never
look back at, build it into a stack, and before pushing the new element ask
whether it annihilates the current top. If it does, pop instead of pushing.

```
input "abbaca", removing adjacent equal pairs

a   → stack [a]
b   → top is a, no match, push      [a, b]
b   → top is b, cancels, pop        [a]
a   → top is a, cancels, pop        []
c   → push                          [c]
a   → push                          [c, a]
result "ca"
```

Path simplification (`/a/./b/../c` → `/a/c`) is the same pattern: `..` pops, a
real directory name pushes.

### Pattern C — the monotonic stack → [[../concepts/monotonic-stack|concept page]]
> **Trigger:** "next greater element", "next smaller", "how many days until",
> "the nearest thing to the left/right that is bigger", "largest rectangle",
> "span"

**Why this is in the lesson when no Blind 75 problem in this topic uses it:**
because it is the *reason* stacks have a reputation as a hard topic, because it
is the single most common follow-up when you finish Valid Parentheses in eight
minutes, and because it shows up all over the adjacent problem sets (Daily
Temperatures, Next Greater Element, Largest Rectangle in Histogram, Trapping
Rain Water's stack solution). You will meet it. Better to meet it named.

The idea: keep the stack **sorted** — every element in it is larger than the one
above it (a decreasing stack), or smaller (an increasing stack). You enforce
that by *popping anything that would violate the order before you push*.

The pay-off is the popping itself. **When you pop element `x` because incoming
element `y` is bigger, you have just discovered that `y` is the next greater
element for `x`.** The pop is not cleanup — the pop *is* the answer.

```
heights [2, 1, 5, 6]  — find the next greater element for each
keeping a DECREASING stack of indices

see 2 → stack empty, push                  [2]
see 1 → 1 < 2, order holds, push           [2, 1]
see 5 → 5 > 1  → pop 1, answer for 1 is 5  [2]
        5 > 2  → pop 2, answer for 2 is 5  []
        push                               [5]
see 6 → 6 > 5  → pop 5, answer for 5 is 6  []
        push                               [6]
end   → 6 left over: no next greater
```

**Why it is O(n), not O(n²):** every element is pushed exactly once and popped
at most once. The inner `while` loop looks alarming but it is paid for by
pushes that already happened. This amortization argument is the thing to be able
to say out loud — it is what separates "I memorised a monotonic stack" from "I
understand one".

### Pattern D — recursion flattened into an explicit stack
> **Trigger:** "do it iteratively", "without recursion", "the input may be very
> deep", plus any traversal of a nested structure

Covered mechanically in section 3. The pattern-recognition half:

- Recursive call → `push` the state you'd have passed as arguments.
- Return → `pop`.
- Base case → the loop condition `while stack:`.
- **Order matters, and it flips.** A stack reverses things. If you want to
  process a node's children left-to-right, you push them **right-to-left**,
  because the last one pushed comes out first. This off-by-a-mirror is the most
  common bug in a first iterative traversal.

Forward reference: this is how you will do iterative pre-order in
[[../curriculum/index|Topic 7]] and iterative DFS in
[[../curriculum/index|Topic 11]]. Swap the stack for a queue and the exact same
loop is BFS.

---

## 6. Python notes

**There is no `Stack` class and you should not write one.** A `list` is the
stack. Using anything else in an interview reads as unfamiliarity with the
language.

```python
stack = []

stack.append(x)        # push          — O(1) amortized
top = stack.pop()      # pop           — O(1)
top = stack[-1]        # peek          — O(1), does NOT remove
if not stack:          # empty check   — O(1), idiomatic
    ...

# guard before touching the top — this is the whole bug surface
if stack and stack[-1] == something:
    stack.pop()
```

The idiom to burn in: **`if stack and <condition on stack[-1]>`**. Python
short-circuits `and`, so the emptiness check protects the index. Write it in
that order every single time and a whole class of `IndexError` disappears.

Two more things worth knowing:

```python
from collections import deque
q = deque()
q.append(x)            # push right
q.popleft()            # O(1) — a LIST's pop(0) is O(n). Use deque for QUEUES.

pairs = {')': '(', ']': '[', '}': '{'}   # close → open
```

That dict is the clean way to express a matching table: a lookup, not a chain of
`if`/`elif`. It is also the [[../concepts/hash-map|hash map]] from Lesson 1
doing unglamorous work — most real uses of a hash map look like this, not like
Two Sum.

```python
import sys
sys.getrecursionlimit()      # ~1000
sys.setrecursionlimit(10**6) # possible, but a red flag in an interview
```

Raising the limit is almost never the right answer to "this recurses too deep".
The right answer is the explicit stack of Pattern D. Skim the
[Python complexity cheat sheet](https://www.geeksforgeeks.org/complexity-cheat-sheet-for-python-operations/)
again for the list operations — `pop()` vs `pop(0)` is right there.

---

## 7. Traps

- **Popping an empty stack.** `IndexError: pop from empty list`. Every `pop()`
  and every `stack[-1]` needs an emptiness guard in front of it. In matching
  problems this is not a defensive nicety — an empty stack at a closing token
  *is* the invalid case, and handling it is part of the algorithm.
- **Forgetting the leftover check at the end.** You survived the whole loop
  without a mismatch, so you return `True` — and `"((("` slips through. The
  answer is `not stack`, not `True`. This is the single most common way to fail
  Valid Parentheses.
- **`pop(0)` instead of `pop()`.** Silently O(n). Makes an O(n) solution O(n²)
  and you won't see it in a passing test.
- **Building a queue out of a list.** Same bug from the other direction: if you
  actually need FIFO, `deque.popleft()` is O(1) and `list.pop(0)` is not.
- **Reaching for a stack when order doesn't matter.** If you never care *which*
  outstanding item is on top — only how many, or whether some value is present —
  a counter or a [[../concepts/seen-set|set]] is the honest structure. A single
  bracket type can be counted with one integer, in O(1) space; a stack there is
  over-engineering, and an interviewer may ask exactly that as a follow-up.
- **Claiming O(1) space.** If you allocated a stack that can grow with the
  input, it is **O(n) space**. Say so unprompted.
- **Forgetting the call stack in a recursive solution's space complexity.**
  O(depth), always, on top of whatever you explicitly allocated.
- **Pushing children in the wrong order** in an iterative traversal. A stack
  reverses. Push right, then left, to visit left first.
- **Pushing values when you needed indices.** In monotonic-stack problems the
  answer is usually "how far away" — which needs positions, not values. Decide
  which you are storing *before* you start typing, and prefer indices; you can
  always get the value from the index, but not the reverse.

---

## 8. Worked example

Not in the problem set, so nothing is spoiled.

> **Problem.** You're keeping score for a game. You get a list of operations,
> applied in order:
> - an integer — record a new score of that value
> - `"+"` — record a new score equal to the sum of the **previous two** scores
> - `"D"` — record a new score equal to **double** the previous score
> - `"C"` — **invalidate** the previous score, removing it from the record
>
> Return the sum of all scores that remain.
> `["5", "2", "C", "D", "+"]` → `5, 2, (remove 2), 10, 15` → sum `30`.

**Brute force.** Keep a list, append and delete from the end, index backwards
into it. That works — but pause on *why* it works, because the reason is the
lesson.

**Spot the pattern.** Every operation refers only to the **most recent** entries,
and one of them **removes the most recent** entry. Nothing ever looks at the
middle of the record. That is LIFO access: this is Pattern B, a stack used as
committed-so-far-with-undo. The moment `"C"` appears in the statement — an undo
— the structure is decided.

```python
def score(ops):
    stack = []
    for op in ops:
        if op == "C":
            stack.pop()                          # undo the last commit
        elif op == "D":
            stack.append(2 * stack[-1])          # peek, don't remove
        elif op == "+":
            stack.append(stack[-1] + stack[-2])  # the top two, still on the stack
        else:
            stack.append(int(op))
    return sum(stack)
```

**Complexity.** O(n) time — each operation does O(1) work, and `sum` is one
final O(n) pass. O(n) space for the stack.

**What to notice.** Three different things happen to the top of the stack and
each one is a different primitive: `"C"` **pops**, `"D"` **peeks** then pushes,
`"+"` peeks *twice* then pushes. Being able to say which of push/pop/peek each
step needs — out loud, before writing it — is the habit that makes stack
problems feel mechanical rather than fiddly.

**The follow-up an interviewer would ask:** "what if `C` arrives first?" The
answer is that the problem's constraints usually rule it out, but you should
*say* that rather than silently assume it. Naming the precondition you're
relying on is free credibility, and it is the same instinct as the emptiness
guard in section 6.

### A second, shorter one — the monotonic shape

Because no Blind 75 problem in this topic will drill Pattern C, do this one on
paper before you move on.

> **Problem.** Given `temps = [73, 74, 75, 71, 69, 72, 76, 73]`, for each day
> return how many days you must wait for a **warmer** day, or 0 if none.

Trace it with a **decreasing stack of indices**, by hand, using the picture in
Pattern C. When a warmer temperature arrives, pop every index it beats — and
each pop's answer is `current_index - popped_index`. Don't look up the code;
the point is to feel *why* the total work is O(n) when the nested loop makes it
look like O(n²). You should be able to finish the trace in five minutes and
state the amortization argument in one sentence.

---

## 9. Materials

Full list on [[../meta/resources|Resources]]. For this lesson, in order:

1. [Stack data structure](https://www.geeksforgeeks.org/stack-data-structure/) — 10 min, the hub page
2. [Stack in Python](https://www.geeksforgeeks.org/stack-in-python/) — 5 min; confirms that a `list` is the answer
3. [Using lists as stacks](https://docs.python.org/3/tutorial/datastructures.html#using-lists-as-stacks) — the official two paragraphs, worth reading for the `pop(0)` warning
4. [`collections.deque`](https://docs.python.org/3/library/collections.html#collections.deque) — skim; this is the queue half of the stack/queue fork
5. [Next greater element](https://www.geeksforgeeks.org/next-greater-element/) — for Pattern C, after you've traced the temperatures example yourself
6. [`sys.setrecursionlimit`](https://docs.python.org/3/library/sys.html#sys.setrecursionlimit) — one paragraph, for the call-stack limit
7. [NeetCode on YouTube](https://www.youtube.com/@NeetCode) — **only after** a timed attempt

For extra reps on this pattern, the [LeetCode 75 plan](https://leetcode.com/studyplan/leetcode-75/)
has a stack section — useful precisely because this topic gives you only one
problem of your own.

---

## 10. Self-check

Answer these **aloud**, in full sentences, before starting the timer.

1. What does LIFO mean, and what kind of problem structure makes LIFO the
   required order rather than just one option?
2. Why is `append`/`pop` at the end of a Python list O(1), but `pop(0)` O(n)?
3. Why is `append` "O(1) amortized" rather than plainly O(1)?
4. What is on the call stack during a recursive call, and what is the space
   complexity of a recursion of depth d?
5. Describe how you would convert any recursive traversal into an iterative one.
   What is the one thing you have to decide?
6. In an iterative traversal, why do you push children in reverse order?
7. What are the *two* distinct ways a bracket string can be invalid, and where in
   the code does each one get caught?
8. What invariant does a monotonic stack maintain, and why is the algorithm O(n)
   despite having a nested loop?
9. When is a stack the *wrong* tool for a problem that looks like it needs one?
10. Give the trigger phrase for each of patterns A–D.

If 4, 5 or 8 come out mumbled, go back — those three are the ones that get
cashed in during Topics 7, 9 and 11.

---

## 11. Ready?

**First timed problem: Valid Parentheses** (Easy) — Pattern A.

Say `timed valid-parentheses` and I'll give you the statement and start the
30-minute box. No hints while it's running.

**What "good" looks like here.** This is a short problem and you may well finish
in under ten minutes, so finishing is not the bar. The bar is:

- You identify it as LIFO **from the statement**, and say why — "the innermost
  open must close first" — before writing anything
- You use a dict for the matching pairs, not a chain of `if`/`elif`
- Every `pop()` and `stack[-1]` has an emptiness guard, written in the
  `if stack and ...` order
- You handle **both** failure modes, and the final return is `not stack`
- You test `""`, `"("`, `")"`, and a correctly-ordered-but-mismatched case like
  `"([)]"` before declaring done
- You state O(n) time **and O(n) space**, unprompted

**If you finish early, do not stop the clock.** This topic has one problem, so
the leftover time is where the topic's real value is. In order:

1. Trace the Daily Temperatures example from section 8 by hand, then write the
   monotonic stack for it from scratch.
2. Write a recursive function that sums a nested list, then rewrite it
   iteratively with an explicit stack. This is Topic 7's homework, done early.

Both go in the [[../meta/review-queue|review queue]] the same way a problem
would. **Repeat until cold-solvable is the default, not the exception** — and
for this topic in particular, "cold-solvable" means Pattern C too, not just the
one problem on the list.
