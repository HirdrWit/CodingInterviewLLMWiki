---
type: lesson
topic: linked-list
status: ready
updated: 2026-09-14
sources: [https://www.geeksforgeeks.org/data-structures/linked-list/, https://www.geeksforgeeks.org/reverse-a-linked-list/, https://www.geeksforgeeks.org/floyds-cycle-finding-algorithm/, https://www.geeksforgeeks.org/complexity-cheat-sheet-for-python-operations/]
tags: [lesson, linked-list]
---

# Lesson 6 — Linked List

**Curriculum:** [[../curriculum/linked-list|Topic 6]] · **Materials:** [[../meta/resources|Resources]]
**Prerequisites:** [[../curriculum/arrays-hashing|Topic 1]] (the array memory model),
[[../concepts/two-pointers|two pointers]] from [[../curriculum/two-pointers|Topic 2]].
**Work through in:** 60–90 minutes. The reversal diagram in section 5 is worth
half of that on its own — do not skim it.

---

## 1. Why this topic

The obvious reason: linked lists show up constantly, and they're the substrate
for the next several topics. A tree is a linked list that branches. A graph is a
linked list that stops pretending it's acyclic. Get comfortable holding node
references in your head now and Topic 7 costs you half as much.

The real reason: **this is the topic where the memory layout stops being
trivia.** In [[../curriculum/arrays-hashing|Topic 1]] you learned that array
indexing is O(1) because the boxes are contiguous and the same width, so the
machine can compute an address instead of searching. Every complexity number on
this page is the consequence of dropping exactly that one property. Nodes are
scattered anywhere in memory and joined by pointers, so:

- you **can't** compute where element `i` is → indexing goes O(1) → **O(n)**
- you **don't** have to shuffle anything to make room → insert at front goes
  O(n) → **O(1)**

That's the whole trade, and "array vs linked list, when would you use each" is
one of the most-asked questions in a phone screen. Most candidates recite the
table. The ones who get callbacks explain *why* the table looks like that, from
the memory layout. That's what section 2 and 3 are for.

The third thing this topic teaches, which transfers further than the data
structure itself: **problems where the answer is a pointer choreography, not an
algorithm.** There's nothing clever to discover in reversing a list. The
difficulty is entirely in not losing your grip on a node. That skill — reasoning
precisely about references and the order of assignments — is the same skill that
makes tree manipulation and in-place array work feel calm instead of frantic.

---

## 2. The mental model

### An array is a street. A linked list is a treasure hunt.

In Topic 1 the array was a street of numbered houses, all the same width, laid
end to end. You get to house 4 with arithmetic.

A linked list is a chain of clues. Each stop holds a **value** and a **piece of
paper telling you where to go next**. The stops are scattered all over the city
— no order, no pattern, addresses unrelated to each other.

```
head
 │
 ▼
┌─────┬───┐   ┌─────┬───┐   ┌─────┬───┐   ┌─────┬──────┐
│  7  │ ●─┼──▶│  2  │ ●─┼──▶│  9  │ ●─┼──▶│  4  │ None │
└─────┴───┘   └─────┴───┘   └─────┴───┘   └─────┴──────┘
 @4210         @9008         @1112         @7734
 val  next     val  next     val  next     val  next
```

Note the addresses: `4210`, `9008`, `1112`, `7734`. Meaningless. There is no
formula from `i` to an address, so **there is no way to reach node 3 except by
walking from `head` through nodes 0, 1, 2.**

Two consequences, and everything else follows from them:

**You only ever hold what you're holding.** The only nodes you can touch are the
ones you have a variable pointing at, plus whatever you can reach by following
`next` from them. There is no going back — a node has no pointer to its
predecessor. **If you drop a reference before you're done with it, that part of
the list is gone.** In Python it's garbage collected; in C it leaks. Either way
you can't get it back.

**Rearranging is free; finding is expensive.** To splice a new node in after a
node you already hold, you rewrite two pointers. Nothing moves in memory, nothing
shuffles. Compare the array, where inserting at the front means relocating every
single element. The linked list pays for that freedom up front, in the cost of
ever *finding* anything.

### Insertion, as a picture

Inserting `5` after the node holding `2`:

```
before:   │  2  │ ●─┼──────────────▶│  9  │
                                     
new node:              ┌─────┬───┐
                       │  5  │ ? │
                       └─────┴───┘

step 1:   new.next = curr.next        (point the newcomer at 9)
step 2:   curr.next = new             (point 2 at the newcomer)

after:    │  2  │ ●─┼──▶│  5  │ ●─┼──▶│  9  │
```

**The order of those two steps is the entire lesson of this topic.** Do step 2
first and `curr.next` no longer points at `9` — you've just lost the rest of the
list, and you have no way to find it again. See
[[../concepts/pointer-rewiring|pointer rewiring]].

---

## 3. The mechanics — enough to reason about cost

**The node is the whole data structure.** There is no container object doing
bookkeeping. A list *is* a reference to its first node.

```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next
```

That's it. `head` is a variable holding one `ListNode`. The "list" is an emergent
property of nodes pointing at each other. A `None` in the `next` field is the
end.

**There is no length.** Nothing stores it. `len(linked_list)` doesn't exist;
counting is an O(n) walk. So if a problem needs the length, walking the list to
get it costs a full pass — which is exactly why
[[../concepts/fast-slow-pointers|fast/slow pointers]] exist, to answer
length-shaped questions in one pass instead of two.

**There is no bounds check and no "index out of range".** Walking off the end
means dereferencing `None`, which in Python is
`AttributeError: 'NoneType' object has no attribute 'next'`. That error, in an
interview, is almost always one of two things: you advanced without checking, or
you checked `curr` when you should have checked `curr.next`.

**Memory cost is real.** Each node carries a pointer alongside its value. For a
list of 64-bit integers, that's an extra 8 bytes per element minimum — the
structure is roughly 2× the array for the same data, before Python's per-object
overhead, which makes it far worse than 2×. Plus the nodes are scattered, so
walking one thrashes the CPU cache while walking an array streams through it.
**In practice, for the same operation count, an array is faster than a linked
list even where the big-O says otherwise.** Interviewers like this point because
it shows you know big-O isn't the whole story.

**Singly vs doubly.** Everything in this topic is *singly* linked — one `next`
pointer, one direction. A doubly linked list adds a `prev` pointer, which buys
O(1) deletion of a node you hold (you can reach its predecessor) and backward
traversal, at the cost of another pointer per node and twice as many pointers to
keep consistent on every edit. Python's `collections.deque` is a doubly linked
list of blocks; that's why `appendleft` is O(1) and `list.insert(0, x)` is O(n).

**Variants worth naming, because interviewers ask:** a **circular** list has the
tail pointing back at the head instead of `None` — which is exactly the thing
problem 5 asks you to detect. A list with a **sentinel** (dummy) head has a real
node in front of the first real element so that "insert at front" and "insert in
the middle" become the same code path. See [[../concepts/dummy-head|dummy head]].

---

## 4. Complexity — the table to know cold

Read this column-against-column with the array. Every difference traces back to
"contiguous and same-width" versus "scattered and chained".

| Operation | Array / Python list | Singly linked list |
|---|---|---|
| Access element `i` | **O(1)** — computed address | O(n) — must walk |
| Search for a value | O(n) | O(n) |
| Insert / delete at **front** | O(n) — shuffle everything | **O(1)** — rewrite one pointer |
| Insert / delete at **back** | **O(1)** amortized | O(n) — must walk to find the tail |
| Insert / delete **after a node you hold** | O(n) — shuffle | **O(1)** |
| Get the length | **O(1)** — stored | O(n) — count them |
| Memory per element | value only | value + pointer, scattered |
| Cache behaviour | streams, fast | jumps, slow |

Three things to take from it:

1. The linked list wins **exactly one** thing: O(1) insert and delete at a
   position you already have a reference to. Everything else is a loss or a tie.
2. "Insert at back is O(1)" is a lie *unless you keep a tail pointer.* Many
   implementations do; the bare `head`-only list in these problems does not.
3. **Reversal, merging, and reordering are all O(1) space** on a linked list
   because you rearrange pointers instead of moving data. That's the reason
   these problems exist — they're the cleanest possible test of whether you can
   manipulate references without a safety net.

---

## 5. The patterns

The most valuable section on the page. Each is **a trigger in the problem
statement → the tool it should summon.**

### Pattern A — three-pointer reversal → [[../concepts/pointer-rewiring|concept page]]
> **Trigger:** "reverse the list", "reverse a portion", "in reverse order" with O(1) space

The one piece of pointer choreography you must be able to produce from muscle
memory, because three other problems in this topic use it as a sub-step.

The idea: walk forward, and as you pass each node, turn its arrow around. You
need **three** references at all times — the node before, the node you're at,
and the node after — because the instant you flip `curr.next` backwards, the
forward path is destroyed and `next` is your only way to continue.

Step by step on `1 → 2 → 3 → None`:

```
start:     prev = None      curr = 1

           None    ┌───┐    ┌───┐    ┌───┐
           prev    │ 1 │───▶│ 2 │───▶│ 3 │──▶ None
                   └───┘    └───┘    └───┘
                   curr

step 1:    nxt = curr.next                     ← SAVE before you break anything
                   ┌───┐    ┌───┐    ┌───┐
           None    │ 1 │───▶│ 2 │───▶│ 3 │──▶ None
                   └───┘    └───┘    └───┘
                   curr     nxt

step 2:    curr.next = prev                    ← flip the arrow
                   ┌───┐    ┌───┐    ┌───┐
           None ◀──│ 1 │    │ 2 │───▶│ 3 │──▶ None
                   └───┘    └───┘    └───┘
                   curr     nxt
           (1 is now detached from 2 — but nxt still holds 2, so we're fine)

step 3:    prev = curr                         ← shuffle both forward
step 4:    curr = nxt
                   ┌───┐    ┌───┐    ┌───┐
           None ◀──│ 1 │    │ 2 │───▶│ 3 │──▶ None
                   └───┘    └───┘    └───┘
                   prev     curr

after next round:
                   ┌───┐    ┌───┐    ┌───┐
           None ◀──│ 1 │◀───│ 2 │    │ 3 │──▶ None
                   └───┘    └───┘    └───┘
                            prev     curr

after last round:
                   ┌───┐    ┌───┐    ┌───┐
           None ◀──│ 1 │◀───│ 2 │◀───│ 3 │      curr = None
                   └───┘    └───┘    └───┘
                                     prev  ← the new head
```

**The loop ends when `curr` is `None`, and the answer is `prev`, not `curr`.**
Returning `curr` returns `None`; returning `head` returns a one-node list.
Both are extremely common and both look like a correct solution until you test.

The shape, in four lines and a fixed order:

```
nxt = curr.next     # save
curr.next = prev    # flip
prev = curr         # advance
curr = nxt          # advance
```

Learn those four lines in that order as a single unit. There is no reason to
re-derive them under interview pressure.

### Pattern B — the dummy head → [[../concepts/dummy-head|concept page]]
> **Trigger:** "merge", "build a new list", "the head itself might be removed", "return the resulting list"

Building or filtering a list has an annoying asymmetry: the first node is special
because there's nothing in front of it to point at it. That forces an `if it's
the first one, do this instead` branch through code that is otherwise uniform —
and that branch is where the bugs live.

The fix: **fabricate a node in front of the real list.**

```
      dummy
        │
        ▼
    ┌──────┬───┐
    │ junk │ ●─┼──▶  (the real list gets built here)
    └──────┴───┘

    return dummy.next    ← never dummy
```

Now every insertion is "attach to the node I'm holding", with no special case,
and the real head is whatever ends up at `dummy.next` — which you don't have to
decide in advance. The cost is one throwaway node and remembering to return
`dummy.next`.

Pair it with a **tail pointer** (often called `curr`) that walks along behind,
always pointing at the last node of the output so far. Attaching is then
`tail.next = node; tail = tail.next`.

### Pattern C — two-pointer merge
> **Trigger:** "merge two sorted lists", "combine in sorted order", "splice together"

Direct descendant of [[../concepts/two-pointers|two pointers]] from Topic 2, but
with a twist that matters: on arrays you *copy* values into a new array; on
linked lists you **relink existing nodes** into a new order. No allocation, O(1)
extra space.

Hold a pointer into each input list. Compare heads, attach the smaller to your
output tail, advance that list's pointer only. Repeat until one runs out — then
**attach the whole remainder of the other in one move**, because it's already
sorted and already linked. That last step is the tell that you understood the
structure: a candidate who copies the remainder node by node has been thinking
in arrays.

### Pattern D — fast and slow pointers → [[../concepts/fast-slow-pointers|concept page]]
> **Trigger:** "the middle of the list", "the nth from the end", "is there a cycle", any positional question **in one pass**

The fix for "there is no length and no going backwards". Run two pointers over
the same list at different rates or with a fixed head start, and read the answer
off their relationship.

**Different speeds** — `slow` one step, `fast` two. When `fast` hits the end,
`slow` is at the middle, because it has gone exactly half as far.

```
        ┌──┐  ┌──┐  ┌──┐  ┌──┐  ┌──┐
        │ 1│─▶│ 2│─▶│ 3│─▶│ 4│─▶│ 5│─▶ None
        └──┘  └──┘  └──┘  └──┘  └──┘
start:  s,f
step 1:       s          f
step 2:             s                f=None → stop, s is the middle
```

**Fixed offset** — advance `fast` k steps first, then move both at the same
rate. When `fast` hits the end, `slow` is exactly k from the end. The gap
between them never changes; that's the whole mechanism, and it turns "I'd need
the length to know which node that is" into a single pass.

**Cycle detection (Floyd's)** — if the list loops, a 2-speed pointer and a
1-speed pointer are on a circular track. The fast one gains exactly one position
per step on the slow one, so it cannot jump over it: it *must* land on it. If
there's no cycle, `fast` runs off the end. So: they meet → cycle; `fast`
reaches `None` → no cycle. O(n) time, **O(1) space** — and the O(1) is the
point, because the obvious solution is a
[[../concepts/seen-set|seen-set]] of visited nodes, which is O(n) space and
which the interviewer will ask you to beat.

### Pattern E — decompose into sub-lists, then recombine
> **Trigger:** "reorder", "rearrange into a specific interleaving", "rotate", "partition around a value"

Rearrangement problems look intimidating because the target order seems to need
random access. The move is to **stop trying to do it in one pass** and instead
break the task into two or three operations you already have:

```
find the middle          (Pattern D)
    ↓
split into two lists
    ↓
reverse the second half  (Pattern A)
    ↓
interleave the two       (Pattern C, alternating instead of comparing)
```

Each step is something you can already write cold. Saying this decomposition out
loud *before* writing any code is what a strong candidate does here — it turns a
problem that looks like it needs invention into three known moves in sequence.

### Pattern F — merging many lists
> **Trigger:** "k sorted lists", "k sorted anything", "merge all of them"

Two routes, and you should be able to name both.

**Pairwise merging.** Merge list 1 with list 2, that result with list 3, and so
on — but that's O(k·n) because the accumulating list gets re-walked every time.
The fix is to merge in **rounds**: pair them up, merge each pair, halving the
number of lists each round. `log k` rounds, each touching all N nodes total:
**O(N log k)**, O(1) extra space. This uses nothing but Pattern C.

**A min-heap.** Push the head of each list into a heap of size k, pop the
smallest, attach it, and push that node's successor. Same **O(N log k)**, with
O(k) space.

> **Forward dependency:** heaps are [[../curriculum/index|Topic 8]], which you
> haven't done. Do not let that block this problem — **the pairwise/rounds route
> needs no new data structure and hits the same complexity.** Solve it that way,
> and mention the heap alternative aloud as the thing you'd reach for if the
> lists were arriving as a stream. Coming back to re-solve it with a heap after
> Topic 8 is a good use of a review slot.

---

## 6. Python notes

There's no built-in linked list — you define the node class, and LeetCode gives
it to you pre-defined in the stub. Know it by heart anyway:

```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next
```

The idioms that matter:

```python
dummy = ListNode()              # junk node in front; return dummy.next at the end
tail = dummy                    # walks behind, always at the last output node

while curr:                     # "while there is a node"
while curr and curr.next:       # "while there is a node AND one after it"

a, b = b, a                     # simultaneous assignment — RHS evaluated first
prev, curr = curr, nxt          # which is why this is safe in one line
```

**Two Python-specific things worth knowing:**

**Assignment copies the reference, never the node.** `p = head` gives you a
second name for the same object; mutating `p.val` mutates the list. But
`p = p.next` only moves *your* variable — it doesn't touch the list at all.
Distinguishing "I moved my pointer" from "I changed the structure" is most of
debugging these problems.

**`and` short-circuits, and you depend on it.** `while fast and fast.next` works
precisely because Python never evaluates `fast.next` when `fast` is `None`.
Write the conditions in that order every time; reversed, it crashes.

**Don't build a Python list of nodes to make it easy.** Dumping the list into an
array, solving it there, and rebuilding is O(n) space and defeats the point of
the problem. It's a legitimate thing to *mention* as the brute force — and then
beat.

---

## 7. Traps

- **Losing the rest of the list.** Reassigning a `next` before saving what it
  pointed at. This is the number one linked-list bug, it is silent, and it
  produces either a truncated list or an infinite loop. **Save, then break.**
- **Returning the wrong variable after a reversal.** The answer is `prev`.
  `curr` is `None` and `head` is now the *tail*.
- **`curr.next` vs `curr` in the loop condition.** `while curr` is for
  "process every node". `while curr and curr.next` is for "I need a pair" or
  "I'm going to look one ahead". Getting this wrong is an `AttributeError` on
  the last node, or a skipped final element.
- **Forgetting to terminate the new list.** After rearranging, some node must
  have `next = None`. If nothing does, you've built a cycle and the test harness
  hangs. Splitting a list in half means explicitly setting the first half's last
  node's `next` to `None`.
- **Returning `dummy` instead of `dummy.next`.** Your output has a junk `0` at
  the front. Reliably embarrassing.
- **Not handling an empty or single-node list.** `head is None` and
  `head.next is None` break more linked-list solutions than any algorithmic
  error. Test both before saying you're done — every time.
- **Two-node lists in fast/slow problems.** The off-by-one in "which node is the
  middle" only shows up at even lengths. Decide up front whether you want the
  first or second middle, and check it on `[1,2]` by hand.
- **Comparing nodes by value when you mean identity.** In cycle problems the
  question is "is this the same node", which is `is`, not `==`. Two nodes can
  hold equal values and be different objects.
- **Claiming O(1) space while using recursion.** A recursive reversal or merge
  is O(n) *stack* space. It's a fine solution — just don't call it constant
  space, because the interviewer is waiting for exactly that.

---

## 8. Worked example

Not from the problem set, so nothing is spoiled.

> **Problem.** Given the head of a linked list and a value `val`, remove **all**
> nodes with that value and return the new head.
> `1 → 2 → 6 → 3 → 6`, `val = 6` → `1 → 2 → 3`.

**State the difficulty first.** Deleting a node means making its *predecessor*
point past it — and a singly linked list gives you no way back to a predecessor.
So the walk has to carry the predecessor along: you stand on a node and look at
`curr.next`, deciding whether to skip it.

**Then the second difficulty, which is the real one.** What if the head itself
holds `val`? It has no predecessor. Now you need a special case in front of the
loop, possibly repeated (`1 → 6 → 6 → 2`, `val=6`... no, `6 → 6 → 1`). That
branch is where the bug will be.

**Spot the pattern.** "The head itself might be removed" is the trigger for
**Pattern B** — a [[../concepts/dummy-head|dummy head]] gives the real head a
predecessor, and the special case evaporates.

```python
def remove_elements(head, val):
    dummy = ListNode(0, head)          # fabricate a predecessor for head
    prev = dummy

    while prev.next:                   # look one ahead, so guard .next
        if prev.next.val == val:
            prev.next = prev.next.next # splice it out; do NOT advance prev
        else:
            prev = prev.next           # keep it; move on

    return dummy.next                  # NOT dummy
```

**The one subtle line:** when you remove a node you must *not* advance `prev`,
because the new `prev.next` is a node you haven't examined yet. Advancing there
is how `6 → 6` loses the second one. Say that out loud in the interview — it's
the detail that shows you traced it rather than pattern-matched it.

**Complexity.** O(n) time, one pass. **O(1) space** — the dummy is a single
node, not proportional to the input.

**Edge cases to state unprompted:** empty list (`dummy.next` is `None`, loop
never runs, returns `None` — correct for free), every node matches (returns
`None` — also correct for free). That's the mark of a good structural choice:
the edge cases stop being special.

**The move to internalise:** when the first element is special, stop writing a
branch for it. Manufacture a predecessor and make it ordinary.

---

## 9. Materials

Full list on [[../meta/resources|Resources]]. For this lesson, in order:

1. [Linked List data structure](https://www.geeksforgeeks.org/data-structures/linked-list/) — the hub page; read the intro and the array-vs-linked-list comparison
2. [Reverse a linked list](https://www.geeksforgeeks.org/reverse-a-linked-list/) — 15 min. Trace the iterative version on paper until you can produce it without the page
3. [Floyd's cycle-finding algorithm](https://www.geeksforgeeks.org/floyds-cycle-finding-algorithm/) — the proof of *why* fast and slow must meet
4. [Python complexity cheat sheet](https://www.geeksforgeeks.org/complexity-cheat-sheet-for-python-operations/) — re-skim the list section, and note why `deque` exists
5. [NeetCode on YouTube](https://www.youtube.com/@NeetCode) — search the problem name, but **only after** a timed attempt and a debrief
6. Optional: [LeetCode 75](https://leetcode.com/studyplan/leetcode-75/) has a linked-list section — extra reps if reversal won't stick

**Do one thing away from the screen:** draw a five-node list on paper and
physically trace the reversal, rewriting the arrows with a pencil. Pointer bugs
come from trying to hold three references in your head at once. On paper you
can't lose them.

---

## 10. Self-check

Answer these **aloud**, in full sentences, before starting the timer.

1. Why is array indexing O(1) but linked-list indexing O(n)? Answer from the
   memory layout, not from the table.
2. Why is insert-at-front O(1) on a linked list but O(n) on an array?
3. When would you actually choose a linked list over an array in real code?
   (Name a case where the big-O advantage survives the cache penalty.)
4. In the three-pointer reversal, why do you need the third pointer at all?
   What breaks if you drop it?
5. After the reversal loop ends, which variable holds the new head, and why not
   the other two?
6. What does a dummy head buy you, and what must you remember to return?
7. Explain why a fast and a slow pointer *must* meet if there's a cycle — why
   can't the fast one skip over the slow one?
8. How do you find the node k from the end in a single pass?
9. What's the space complexity of a *recursive* list reversal, and why isn't it
   O(1)?
10. Give the trigger phrase for each of patterns A–F.

---

## 11. Ready?

**First timed problem: Reverse Linked List** (Easy) — Pattern A.

Say `timed reverse-linked-list` and I'll give you the statement and start the
30-minute box. No hints while it's running.

**What "good" looks like here.** This is a short problem, so finishing isn't the
bar — the bar is finishing *cleanly and defensibly*:

- You draw the box-and-arrow diagram before writing code. Three nodes is enough.
- You name the three pointers meaningfully — `prev`, `curr`, `nxt` — and you can
  say what each one is for.
- You get the four lines in the right order **first try**, without a crash that
  tells you the order was wrong.
- You return `prev`, and you can say why.
- You test `None` and a single node before declaring done.
- You state O(n) time, O(1) space, and you volunteer that the recursive version
  is O(n) space.

If you finish well inside 30 minutes, roll straight into **Merge Two Sorted
Lists** — it's Pattern B plus Pattern C and it's the natural companion. Two
Easies in one box is a good session.

The three Mediums and the Hard in this topic are all built out of Patterns A–D.
Nothing new gets introduced after problem 2 — it's recombination. So the time
you spend making reversal automatic is the highest-leverage time in the topic.

Not finishing is fine and expected on the Mediums. It goes in the
[[../meta/review-queue|review queue]] and comes back around. **The plan is to
repeat until it's cold-solvable, not to clear the list once.**
