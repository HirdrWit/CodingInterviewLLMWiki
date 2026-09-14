---
type: concept
status: unseen
updated: 2026-09-14
sources: [wiki/lessons/18-bit-manipulation.md]
tags: [bit-manipulation]
---

# Two's Complement

**In one sentence:** If a register is just 32 switches, the minus sign has to
live in one of them — so the top bit is given a place value of **negative** 2³¹
while every other bit keeps its normal positive value, and everything else
follows from that single flip.

§3.4 of [[../lessons/18-bit-manipulation|Lesson 18]].

## When to reach for it

Trigger: "32-bit", "signed", "unsigned", "`INT_MAX` / `INT_MIN`", "overflow",
"reverse the bits", or any problem where a negative input is possible and you're
working on the bit pattern rather than the value.

Also: any time a Python bit-manipulation loop **doesn't terminate**. That is
almost always this concept, unlearned.

## How it works

```
bit:       31     30  ...   2    1    0
place:  -2^31   +2^30  ...  +4   +2   +1
```

One column carries a negative weight; the rest are ordinary. Four consequences,
all worth having ready:

- **`-1` is all ones.** `-2³¹ + 2³⁰ + ... + 2 + 1 = -1`. Every switch on. Commit
  this to memory — `-1` used as a mask means "every bit".
- **Ordinary binary addition just works on negatives**, with no special case.
  That is the entire reason this representation won.
- **Negation is `-n == ~n + 1`** — flip every bit, add one.
- **The range is asymmetric: `-2³¹ .. 2³¹ - 1`.** There is one more negative
  number than positive, because zero occupies a slot on the positive side.
  "What about `INT_MIN`?" is a standard follow-up, and it is asked because
  `-INT_MIN` overflows.

## Arithmetic vs logical right shift

When you shift right, what comes in at the top?

- **Arithmetic shift** copies the **sign bit** in, preserving the sign. `-8 >> 1`
  is `-4`. This is what `>>` does in Python, Java, and C on signed types.
- **Logical shift** brings in **zeros** regardless, treating the pattern as
  unsigned. Java spells it `>>>`.

**Python has no logical shift operator and no unsigned type.** `>>` is always
arithmetic, so a negative stays negative forever. This is what makes "reverse
the bits of a 32-bit unsigned integer" subtler in Python than it looks.

## The Python warning

**Python's integers are arbitrary-precision, and this entire topic assumes they
aren't.** That mismatch is the single biggest source of wrong answers here.

1. **Nothing overflows or wraps.** `1 << 100` is a perfectly good number. A loop
   that "terminates when the carry shifts off the end of the word" never
   terminates, because there is no end of the word.
2. **Negatives behave as an endless run of 1s extending leftward.** So
   `~5 == -6`, and `-1` has no highest set bit. You cannot read a negative
   Python int's bit pattern directly: `bin(-5)` gives `'-0b101'` — a minus sign
   and a magnitude, not two's complement.
3. **`while n:` on a negative never terminates**, because shifting right
   converges to `-1`, not `0`.

The fix is to **impose the width yourself**, in two halves. Both halves are
required; neither alone is enough.

```python
MASK    = 0xFFFFFFFF      # 32 ones — keeps only the low 32 bits
INT_MAX = 0x7FFFFFFF      # largest positive 32-bit signed value

x &= MASK                 # half 1: truncate, emulating hardware wraparound
```

Mask **after every step that could grow the number**, not just at the end.

```python
if x > INT_MAX:           # half 2: reinterpret as negative
    x = ~(x ^ MASK)
```

After masking, Python holds a *non-negative* number in `0 .. 2³²-1` — but a
32-bit signed value above `INT_MAX` is supposed to be negative. `x ^ MASK` flips
the low 32 bits and `~` supplies the sign: the §3.4 conversion run backwards.
Sit with why that's equivalent to "subtract 2³²" — both framings are correct and
one of them will click.

**Forgetting half 2 is why a solution passes the positive test cases and fails
the negative ones.**

## Complexity

O(1). Masking and sign conversion are a constant handful of operations on a
fixed-width value, and add nothing asymptotically to whatever loop they wrap.

## Gotchas

- **Masking without converting the sign.** Right bits, wrong sign.
- **`while n:` on a negative Python int.** Infinite. Mask first.
- **Assuming overflow.** In Python there is none; in Java/C++ a 32-bit `int`
  wraps silently past ±2³¹. If asked to port, this breaks first. The ready
  answer: use a 64-bit `long` for intermediates, check the bound before
  multiplying, or reformulate — compare `a > limit / b` rather than
  `a * b > limit`.
- **`bin()` on a negative.** Gives sign-and-magnitude, not the bit pattern you
  want. Use `format(x & 0xFFFFFFFF, '032b')`.
- **Expecting `>>>`.** It doesn't exist in Python.

## Related

- [[bitwise-operators]] — the operators this fixes the semantics of, especially
  `~` and `>>`
- [[n-and-n-minus-1]] — `n & -n` isolates the lowest set bit, and it works
  *because* `-n == ~n + 1`
- [[xor-cancellation]] — `x ^ MASK` here is XOR used as a bulk flip rather than
  as a cancellation device
- [[constraint-bounded-complexity]] — "O(1) because the width is a fixed 32 bits"
  is the same reasoning: a bounded input turns a loop into a constant

## Evidence

Problems where I used this unaided:
