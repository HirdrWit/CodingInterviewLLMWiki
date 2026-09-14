---
type: lesson
topic: bit-manipulation
status: ready
updated: 2026-09-14
sources: [https://www.geeksforgeeks.org/bitwise-algorithms/, https://docs.python.org/3/library/stdtypes.html#bitwise-operations-on-integer-types]
tags: [lesson, bit-manipulation]
---

# Lesson 18 — Bit Manipulation

**Curriculum:** [[../curriculum/bit-manipulation|Topic 18]] · **Materials:** [[../meta/resources|Resources]]
**Builds on:** nothing, really — this is the one topic in the list that stands alone.
**Work through in:** 45–60 minutes. It's five problems and four of them are Easy.
The time goes into §3, not into the problems.

---

## 1. Why this topic

The obvious reason: five problems, four Easies, and each one has a three-line
answer once you know the trick. Cheap points.

The real reason is different, and it's worth being honest about because it
changes how you should spend the hour.

**Every other topic in this list treats an integer as an atom.** A value you
compare, add, index with, hash. Seventeen topics of that. This topic is the only
place where you open the box and the *representation* — the actual row of bits
sitting in the register — becomes the thing you operate on. That's a genuinely
different mode of thinking, and most people are shaky here not because the
tricks are hard but because **they never actually internalised that an integer
*is* a bit pattern.** They know it as a fact they'd agree with, not as a picture
they can read off.

So the goal of this lesson is not five tricks. It's fluency: being able to look
at `x & (x - 1)` and *see* the bits move, the way you can look at `for i, v in
enumerate(nums)` and see the scan.

And the honest interview note, stated plainly so you budget accordingly:
**bit manipulation is the least transferable topic in the set.** Sliding window
teaches you a way of thinking that shows up in fifty problems. Bit tricks mostly
get asked as *recall* — you either know that `n & (n-1)` clears the lowest set
bit or you don't, and no amount of on-the-spot cleverness reliably derives it
under pressure. Treat this topic as: learn the six operators until they're
reflex, memorise three or four identities, move on. Do not spend a week here.

Where it *does* pay off beyond these five problems: bitmask state in dynamic
programming over subsets, permission and flag fields in real backend code,
hash and checksum internals, and any interviewer follow-up of the form "now do
it in O(1) space".

---

## 2. The mental model

### An integer is a row of light switches

Thirty-two switches in a row. Each switch has a fixed value printed under it —
the powers of two, doubling right to left. The number is the sum of the values
under the switches that are **on**.

```
bit index:   7    6    5    4    3    2    1    0
           ┌────┬────┬────┬────┬────┬────┬────┬────┐
switches:  │ 0  │ 0  │ 0  │ 1  │ 0  │ 1  │ 1  │ 0  │
           └────┴────┴────┴────┴────┴────┴────┴────┘
place:      128   64   32   16    8    4    2    1
                            ▲         ▲    ▲
                           16    +    4  + 2   =  22
```

Three things to take from the picture and never lose:

- **Bit `i` is worth `2^i`.** Bit 0 is the rightmost, worth 1. Off-by-one errors
  in this topic are almost always someone counting bits from the left.
- **The switches are independent.** `&`, `|`, `^`, `~` operate on each column
  separately and simultaneously. There is no carry, no interaction between
  columns. That is *the* property that makes bit ops O(1) and that makes them
  worth reaching for.
- **Shifting slides the whole row sideways**, which multiplies or divides by
  powers of two, because every switch moves to a place worth twice (or half) as
  much.

The only operation in this topic that breaks column independence is **addition**,
which needs carries — and that is exactly why problem 5 is the Medium.

---

## 3. The mechanics

This section is the lesson. Work through each example on paper.

### 3.1 The six operators, in bit columns

**AND (`&`) — 1 only where *both* are 1.** Reads as "keep only what they agree
on". This is the masking operator.

```
   12   0 0 0 0 1 1 0 0
 & 10   0 0 0 0 1 0 1 0
 ----   ---------------
    8   0 0 0 0 1 0 0 0
```

**OR (`|`) — 1 where *either* is 1.** Reads as "merge". This is the set-a-bit
operator.

```
   12   0 0 0 0 1 1 0 0
 | 10   0 0 0 0 1 0 1 0
 ----   ---------------
   14   0 0 0 0 1 1 1 0
```

**XOR (`^`) — 1 where they *differ*.** Reads as "what changed". Do not read it
as "exclusive or" and leave it there; read it as a **difference detector**, and
half this topic falls out.

```
   12   0 0 0 0 1 1 0 0
 ^ 10   0 0 0 0 1 0 1 0
 ----   ---------------
    6   0 0 0 0 0 1 1 0
```

**NOT (`~`) — flip every switch.** In a fixed 32-bit world this is
straightforward. In Python it is not, and §6 explains why.

```
 ~  12   ...1 1 1 1 0 0 1 1     which is -13, see §3.4
```

**Left shift (`<<`) — slide left, fill with zeros.** `x << k == x * 2**k`.

```
   5 << 2
   5    0 0 0 0 0 1 0 1
   20   0 0 0 1 0 1 0 0      each switch moved two places up in value
```

**Right shift (`>>`) — slide right, drop what falls off the end.**
`x >> k == x // 2**k` for non-negative `x`, and note that it's *floor* division:
the bits that fall off the right are simply lost, so it rounds down, always.

```
   20 >> 2
   20   0 0 0 1 0 1 0 0
   5    0 0 0 0 0 1 0 1
```

### 3.2 Masking: reading, setting, clearing one bit

A **mask** is a number you build for the sole purpose of selecting columns. The
mask for bit `i` is `1 << i` — a single 1 in position `i`, zeros everywhere else.

```python
mask = 1 << i          # 0...010...0   with the 1 at position i

x & mask               # non-zero iff bit i of x is set   → read
(x >> i) & 1           # same test, gives exactly 0 or 1  → read, normalised
x | mask               # bit i forced on                  → set
x & ~mask              # bit i forced off                 → clear
x ^ mask               # bit i flipped                    → toggle
```

Memorise the shapes, not the lines: **`&` reads and clears, `|` sets, `^`
toggles.** `x & 1` — the special case `i = 0` — tests oddness, and that one
comes up constantly.

### 3.3 The two properties that carry the topic

**Property 1: XOR is its own inverse.** → [[../concepts/xor-cancellation|concept page]]

```
x ^ x == 0          anything XORed with itself vanishes
x ^ 0 == x          zero is the identity
a ^ b ^ a == b      order doesn't matter: XOR is commutative and associative
```

Read the third line carefully, because it's the whole idea: you can XOR things
together **in any order**, and any value that appears an even number of times
cancels itself out of the result. That turns XOR into a **cancellation device**.
Fold a whole pile of numbers together with `^` and what survives is exactly what
didn't pair up.

The second-order consequence, which is the part people miss: because XOR is its
own inverse, you can *cancel a value back out* of an accumulated result later,
without knowing anything else in the pile. Nothing else in this lesson has that
property.

**Property 2: `n & (n - 1)` clears the lowest set bit.** → [[../concepts/n-and-n-minus-1|concept page]]

Not a coincidence, and worth deriving once on a concrete number so you can
re-derive it in an interview instead of just asserting it.

Take `n = 44`:

```
   n       = 44      0 0 1 0 1 1 0 0
   n - 1   = 43      0 0 1 0 1 0 1 1
```

Look at what subtracting one did. The lowest set bit of `n` is at position 2.
Everything **above** it is untouched. The bit **at** position 2 flipped from 1
to 0. Everything **below** it flipped from 0 to 1. That's just borrowing in
binary subtraction: you borrow from the nearest 1 to the left, and the borrow
turns every 0 it passed over into a 1.

Now AND them:

```
   n       = 44      0 0 1 0 1 1 0 0
   n - 1   = 43      0 0 1 0 1 0 1 1
   -----------       ---------------
   n & (n-1) = 40    0 0 1 0 1 0 0 0
```

The high bits agree, so they survive. The lowest set bit is 1-and-0, so it dies.
Everything below is 0-and-1, so it stays dead. **One set bit removed, everything
else identical.** Repeat until zero and you've visited each set bit exactly once.

The sibling identity, from the same borrow behaviour: **`n & -n` *isolates* the
lowest set bit** (gives you a number with only that bit on). Same picture, in
two's complement, because `-n == ~n + 1`.

### 3.4 Two's complement, and why `-1` is all ones

→ [[../concepts/twos-complement|concept page]]

The question this answers: if a register is just 32 switches, where does the
minus sign live?

The answer chosen by every modern machine is **two's complement**: the top bit
(bit 31) carries a place value of **negative** 2³¹, while every other bit keeps
its normal positive value.

```
bit:       31     30  ...   2    1    0
place:  -2^31   +2^30  ...  +4   +2   +1
```

That single sign flip on one column makes everything work:

- `-1` is **all ones**: `-2³¹ + 2³⁰ + ... + 2 + 1 = -1`. Every switch on.
  This is worth committing to memory — `-1` as a mask means "every bit".
- Ordinary binary addition just works on negatives, with no special case. That
  is the entire reason this representation won.
- Negation is `-n == ~n + 1`: flip every bit, add one.
- The range is asymmetric: `-2³¹ .. 2³¹ - 1`. There is one more negative number
  than positive, because zero takes up a slot on the positive side. Interviewers
  do probe this — "what about `INT_MIN`?" is a standard follow-up.

**Arithmetic vs logical right shift.** When you shift right, what comes in at
the top?

- **Arithmetic shift** copies the sign bit in, preserving the sign. `-8 >> 1`
  is `-4`. This is what `>>` does in Python, Java, C on signed types.
- **Logical shift** brings in zeros regardless, treating the pattern as
  unsigned. Java spells it `>>>`. Python **has no logical shift operator** —
  see §6.

This distinction is what makes "reverse the bits of a 32-bit unsigned integer"
subtler in Python than it looks.

### 3.5 Counting bits

The number of set bits is the **population count** (popcount / Hamming weight).
Three ways, in increasing order of cleverness:

1. Check all 32 positions with `(n >> i) & 1`. Always 32 steps.
2. Shift `n` right until it's zero, testing `n & 1` each time. Steps = position
   of the highest set bit, so O(log n).
3. `n & (n - 1)` in a loop. Steps = number of **set** bits — which for sparse
   numbers is much better than either of the above, and is the answer that makes
   an interviewer nod.

---

## 4. Complexity

| Operation | Cost | Why |
|---|---|---|
| `&` `\|` `^` `~` `<<` `>>` on a fixed-width int | **O(1)** | columns are independent; the hardware does all 32 at once |
| Loop over all bits of a 32-bit int | O(32) = **O(1)** | the width is a constant, so say "O(1), or O(w) for width w" |
| Loop `while n: n >>= 1` | **O(log n)** | one step per bit position up to the highest set bit |
| Loop `while n: n &= n - 1` | **O(popcount(n))** | one step per *set* bit — never worse than the above |
| Building an answer for every value `0..n` | **O(n)** if each costs O(1) | this is why the DP-over-bits pattern beats popcounting each |
| Space, for anything on a single int | **O(1)** | the payoff: bit tricks are the classic route to constant space |

The one to get right in an interview: **"O(1) because the word size is a fixed
32 bits"** is the correct framing, not "O(n)". But say the reasoning out loud —
claiming O(1) without naming the fixed width sounds like you got lucky.

---

## 5. The patterns

Five problems, five distinct tools. Trigger first, tool second — the trigger is
the transferable half.

### Pattern A — strip set bits one at a time → [[../concepts/n-and-n-minus-1|concept page]]
> **Trigger:** "how many 1 bits", "Hamming weight", "is it a power of two", "how many steps to turn `a` into `b`"

Loop `n &= n - 1` until `n` is zero and count the iterations. Each pass removes
exactly one set bit, so the work is proportional to the number of ones rather
than to the width.

The power-of-two special case is the same identity read differently: a power of
two has *exactly one* set bit, so `n > 0 and n & (n - 1) == 0` is the whole
test. Recognising that "power of two" and "count the ones" are the same question
is the point.

### Pattern B — DP over bit prefixes
> **Trigger:** "for every number from 0 to n", "compute this for *all* values up to n", an array-of-answers rather than one answer

The naive answer popcounts each value independently: O(n log n). But a number's
bit pattern is *its own pattern with the last bit chopped off, plus that last
bit* — and the chopped-off version is a smaller number whose answer you already
computed.

```
   i = 13      1 1 0 1
   i >> 1 = 6  0 1 1 0        ← same bits, minus the last one
   i & 1  = 1                 ← the bit that fell off
```

So `dp[i] = dp[i >> 1] + (i & 1)`, filled left to right, each entry O(1). This
is the one pattern in the topic that's genuinely transferable — it's the
[[../curriculum/bit-manipulation|bit]] version of the same "reuse the smaller
answer" move that all of topic 13 runs on.

### Pattern C — shift out of one end, shift into the other
> **Trigger:** "reverse the bits", "rotate", "read the bits in the opposite order"

The mechanical shape: keep two registers. Each step, pull the lowest bit off the
source (`src & 1`, then `src >>= 1`) and push it onto the destination
(`dst = (dst << 1) | bit`). Because one is being emptied from the right while
the other fills from the right, the order comes out reversed for free.

Two things to be careful about, and they're where this problem actually gets
missed:

- **The loop must run exactly 32 times, not "until the source is zero".**
  Stopping early silently drops leading zeros, which in a reversal are
  *trailing* significant zeros in the answer.
- **The width is part of the problem.** "Reverse a 32-bit integer" has a
  different answer from "reverse the bits of this number" — see §6 for why that
  bites in Python specifically.

### Pattern D — XOR to cancel everything that pairs up → [[../concepts/xor-cancellation|concept page]]
> **Trigger:** "every element appears twice except one", "one value is missing", "find the element that doesn't have a partner"

If every value in a pile appears an even number of times except one, XOR the
whole pile together and the pairs annihilate. What's left is the odd one out.
O(n) time, **O(1) space** — which is the real selling point, since the obvious
hash-set answer from [[../lessons/01-arrays-hashing|Lesson 1]] costs O(n) space.

The extension that makes it a pattern rather than a trick: you can XOR together
**two different piles** — the values you have and the values you *expected* —
and everything present in both cancels, leaving exactly the discrepancy. That
works even when the two piles are generated differently, e.g. one from an array
and one from a range of indices.

*(There is a second, completely non-bitwise route to the same problem: sum the
range with Gauss's formula `n(n+1)/2` and subtract the actual sum. Know both.
The sum version is more obvious and risks overflow in fixed-width languages; the
XOR version cannot overflow. Being able to offer both, and say why you'd pick
one, is worth more than either alone.)*

### Pattern E — simulate addition with XOR and AND
> **Trigger:** "without using `+` or `-`", "implement arithmetic using only bitwise operations"

The decomposition to know: **addition is XOR plus carry.**

```
   a = 1 0 1 1   (11)
   b = 0 1 1 0   (6)

   a ^ b   = 1 1 0 1     ← the sum, IF there were no carries
   a & b   = 0 0 1 0     ← the columns that generate a carry
   (a & b) << 1          ← a carry belongs to the NEXT column up
```

XOR gives you column-wise addition with the carries thrown away. AND finds
exactly the columns where both bits were 1 — the columns that produce a carry —
and shifting that left by one moves each carry into the column it's owed to.
Then you repeat with the new pair, and you keep repeating until there are no
carries left. It terminates because each round pushes the carries further left,
and there are only so many columns.

That's the mechanism. The loop is yours to write.

### Bonus shape — the bitmask as a set
Not in the Blind 75, but worth knowing it exists: an integer can *be* a set of
up to 32 elements, where bit `i` means "element `i` is present". Union is `|`,
intersection is `&`, difference is `& ~`, membership is `& (1 << i)`. This is
how subset-DP problems represent state, and it's the main reason bit fluency
pays off outside this topic.

---

## 6. Python notes

**Python's integers are arbitrary-precision, and this topic assumes they aren't.**
That mismatch is the single biggest source of wrong answers here. Three
consequences:

**1. Nothing overflows or wraps.** `1 << 100` is a perfectly good number. A loop
that "terminates when the carry shifts off the end of the word" never
terminates, because there is no end of the word.

**2. Negatives are conceptually infinite ones to the left.** Python behaves *as
if* a negative number has an endless run of 1s extending leftward, so `~5 == -6`
and `-1` has no highest bit. You cannot read a negative Python int's bit pattern
directly — `bin(-5)` gives `'-0b101'`, a minus sign and a magnitude, not two's
complement.

**3. There is no logical right shift and no unsigned type.** `>>` is always
arithmetic, so a negative stays negative forever.

The fix, whenever a problem says "32-bit", is to **impose the width yourself**:

```python
MASK = 0xFFFFFFFF          # 32 ones — & with this to keep only the low 32 bits
INT_MAX = 0x7FFFFFFF       # largest positive 32-bit signed value
```

`x & MASK` truncates to 32 bits, emulating the wraparound the hardware would
have done. Do it after every step that could grow the number, not just at the end.

The second half is **converting back**. After masking, Python holds a
*non-negative* number in `0 .. 2³²-1` — but a 32-bit signed value above
`INT_MAX` is supposed to be negative. So you check `if x > INT_MAX` and, if so,
reinterpret: flip the low 32 bits and negate, which is the two's-complement
conversion from §3.4 run backwards. The idiom is `~(x ^ MASK)` — XOR against
all-ones flips the low 32 bits, and `~` supplies the sign. Sit with why that's
equivalent to "subtract 2³²"; both framings are correct and one of them will
click.

You need both halves — masking *and* sign conversion — for problem 5. Neither
alone is enough, and forgetting the second is why a solution passes the positive
test cases and fails the negative ones.

Useful built-ins:

```python
bin(13)            # '0b1101'  — the '0b' prefix is 2 chars, strip or slice it
bin(13)[2:]        # '1101'
format(13, '032b') # '00000000000000000000000000001101' — fixed width, no prefix
int('1101', 2)     # 13
(13).bit_length()  # 4  — position of the highest set bit
(13).bit_count()   # 3  — popcount, Python 3.10+. Know it; expect to be asked
                   #     to write the loop anyway
```

Treat `bit_count()` the way you'd treat `Counter` in topic 1: name it, then
write the manual version, because the manual version is what's being tested.
Reference: [Python's bitwise operations on integers](https://docs.python.org/3/library/stdtypes.html#bitwise-operations-on-integer-types).

---

## 7. Traps

- **Operator precedence.** `&`, `|`, `^` bind *looser* than `==` in Python.
  `x & 1 == 0` parses as `x & (1 == 0)`, which is `x & False`, which is `0`.
  Silently wrong, never raises. **Parenthesise every bitwise sub-expression.**
  This one trap has cost more interviews than every other item on this list.
- **Counting bits from the wrong end.** Bit 0 is the *right*most. Reading a
  diagram left to right and indexing from there is the standard off-by-one.
- **`while n:` on a negative number in Python.** Never terminates — a negative
  int shifts right forever, converging to `-1`, not `0`. Mask first.
- **Stopping a fixed-width loop early.** "Run until the value is zero" and "run
  32 times" are different programs. For reversal, rotation, or anything where
  leading zeros are significant, you need the fixed count.
- **Forgetting the sign conversion.** Masking alone gives you the right *bits*
  with the wrong *sign*. Positive tests pass; negatives fail.
- **Claiming O(n) for a fixed-width loop.** It's O(1) with a constant of 32 —
  but say why, don't just assert it.
- **Reaching for bits when the problem doesn't need them.** If a hash map solves
  it in O(n) and the interviewer hasn't asked for O(1) space, the bit trick is
  showing off. Mention it as the follow-up, implement the readable one.
- **Writing a bit trick with no comment.** `x & (x - 1)` is unreadable to
  anyone who doesn't already know it. One comment line saying *what it does*
  (not how) is the difference between "clever" and "unmaintainable" in the
  interviewer's notes.

---

## 8. Worked example

Nothing from the problem set, so nothing is spoiled.

> **Problem.** Given two integers `left` and `right` with `left <= right`,
> return the bitwise AND of **every** number in the inclusive range
> `[left, right]`. E.g. `left = 5, right = 7` → `5 & 6 & 7 = 4`.

**Brute force.** Loop from `left` to `right` ANDing as you go. Correct, and
hopeless: the range can span billions of values. Say it out loud anyway — it
establishes the baseline and it's where the insight comes from.

**Think in columns.** The rule for AND is: a bit survives only if it is 1 in
*every* number in the range. So ask, column by column, "is this bit 1 in all of
them?"

```
   5    1 0 1
   6    1 1 0
   7    1 1 1
   ---  -----
        1 0 0    = 4
```

Bit 2 is 1 in all three — it survives. Bits 1 and 0 each flip somewhere in the
range — they die. And that's the general truth: **any bit that changes value
anywhere inside the range is guaranteed to be 0 in the result**, because
somewhere in the range it was 0.

**Which bits can't change?** Only the ones in the common leading prefix of
`left` and `right`. Once the two numbers differ at some position, every pattern
below it gets hit somewhere in between, so everything from that position down is
zeroed. The answer is exactly: **the common binary prefix of `left` and `right`,
padded with zeros.**

**Spot the pattern.** "Find the common prefix of two bit patterns" is a shifting
job — Pattern C's machinery, used for a different purpose. Shift both right
until they're equal (that's the prefix), then shift the result back left by the
number of steps you took.

```python
def range_bitwise_and(left, right):
    shifts = 0
    while left < right:          # while they still differ somewhere
        left >>= 1
        right >>= 1
        shifts += 1
    return left << shifts        # the common prefix, zeros restored below it
```

**Complexity.** O(log right) — at most 32 iterations, so O(1) for fixed-width
ints. O(1) space.

**The move to internalise:** when a problem asks about a *whole range* of
numbers, stop thinking about the numbers and start asking what's true of each
bit column across the range. Column-wise thinking is the general skill this
topic is teaching; the specific identities are just the well-known answers.

---

## 9. Materials

Full list on [[../meta/resources|Resources]]. This topic needs less reading than
most — the return is in doing the bit arithmetic by hand.

1. [GeeksforGeeks — Bitwise Algorithms](https://www.geeksforgeeks.org/bitwise-algorithms/)
   — the hub page. Read the operator introduction and the "tricks" list; skip
   the competitive-programming exotica.
2. [Python docs — bitwise operations on integers](https://docs.python.org/3/library/stdtypes.html#bitwise-operations-on-integer-types)
   — short, and the authority on Python's arbitrary-precision behaviour.
3. Search GeeksforGeeks for **"two's complement"** and for **"count set bits in
   an integer"** — the latter walks the naive, shift, and `n & (n-1)` methods in
   order, which is exactly §3.5.
4. [NeetCode on YouTube](https://www.youtube.com/@NeetCode) — one video per
   problem, **only after** a timed attempt and a debrief.
5. Optional, and genuinely useful here: write a five-line helper that prints any
   int as 32 fixed-width binary (`format(x & 0xFFFFFFFF, '032b')`) and keep it
   in your scratch file. Being able to *see* the bits while debugging is worth
   more than any article on this list.

---

## 10. Self-check

Answer these **aloud**, in full sentences, before starting the timer.

1. What is bit `i` worth, and which end do you count from?
2. Give the one-word reading of each of `&`, `|`, `^` — what each is *for*, not
   what its truth table says.
3. How do you read, set, clear, and toggle bit `i`? Write the four expressions
   from memory.
4. Why does `x ^ x == 0` make XOR useful for finding an unpaired element?
5. Walk through `n & (n - 1)` on `n = 44`, in bit columns, and explain why the
   lowest set bit dies and nothing else changes.
6. In two's complement, what place value does the top bit carry, and why does
   that make `-1` all ones?
7. What's the difference between an arithmetic and a logical right shift, and
   which one does Python's `>>` do?
8. Why does `while n: n >>= 1` never terminate for a negative Python int, and
   what do you do about it?
9. What does `0xFFFFFFFF` do, and why isn't masking with it enough on its own?
10. What does `x & 1 == 0` actually evaluate to in Python, and why?
11. Give the trigger phrase for each of patterns A–E.

---

## 11. Ready?

**First timed problem: Number of 1 Bits** (Easy) — Pattern A.

Say `timed number-of-1-bits` and I'll give you the statement and start the
30-minute box. No hints while it's running.

**What "good" looks like here** — this is a short problem, so finishing is not
the bar:

- You write the straightforward shift-and-test loop first and state its
  complexity, *then* offer `n & (n - 1)` as the improvement and say what
  improved (steps proportional to set bits, not to width)
- You state the complexity as "O(1), because the input is a fixed 32 bits" —
  with the reason attached, not just the claim
- You handle `0` and you say out loud what happens for a negative input in
  Python, even if the problem promises unsigned
- The bit trick has exactly one comment explaining what it does
- Clean names: `count`, not `c`

If you finish well inside 30 minutes, roll straight into **Counting Bits** and
use what's left — it's the natural sequel, and seeing the two side by side is
what makes Pattern B land. Three of the five here can reasonably fit in two
boxes.

---

**On finishing the list.** This is topic 18 of 18. When these five are done,
every problem in the Blind 75 will have been *attempted once* — and that is not
the finish line, it's the halfway mark. Per [[../../CLAUDE|the schema]], a
problem counts when it's **re-solved cold on a later day**, and a topic is done
when every problem in it clears that bar. The real scoreboard is
[[../meta/review-queue|the review queue]] emptying, not the curriculum table
filling in.

Expect the earliest topics to have gone stale by the time you arrive here —
that's the system working as designed, not backsliding. Run `review`, take the
demotions honestly, and go round again. **Repeat until confident is the default,
not the fallback.**
