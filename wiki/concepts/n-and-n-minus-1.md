---
type: concept
status: unseen
updated: 2026-09-14
sources: [wiki/lessons/18-bit-manipulation.md]
tags: [bit-manipulation]
---

# `n & (n - 1)`

**In one sentence:** ANDing a number with one less than itself removes exactly
its lowest set bit and leaves every other bit untouched — so looping until zero
visits each set bit once, doing work proportional to the number of **ones**
rather than to the width.

Pattern A and §3.3 of [[../lessons/18-bit-manipulation|Lesson 18]].

## When to reach for it

Trigger: "how many 1 bits", "Hamming weight", "population count", "is it a power
of two", "how many steps to turn `a` into `b`", "how many bits differ".

Recognising that **"power of two" and "count the ones" are the same question** is
the actual transferable bit. A power of two has exactly one set bit, so one
application of the identity empties it:

```python
n > 0 and (n & (n - 1)) == 0      # power of two — note the parentheses
```

## Why it works — derive it, don't assert it

Worth deriving once on a concrete number so you can re-derive it in an interview
rather than reciting it. Take `n = 44`:

```
   n       = 44      0 0 1 0 1 1 0 0
   n - 1   = 43      0 0 1 0 1 0 1 1
```

Look at what subtracting one did. The lowest set bit of `n` is at position 2.

- Everything **above** it is untouched.
- The bit **at** position 2 flipped from 1 to 0.
- Everything **below** it flipped from 0 to 1.

That's just borrowing in binary subtraction: you borrow from the nearest 1 to
the left, and the borrow turns every 0 it passed over into a 1.

Now AND them:

```
   n         = 44    0 0 1 0 1 1 0 0
   n - 1     = 43    0 0 1 0 1 0 1 1
   -------------     ---------------
   n & (n-1) = 40    0 0 1 0 1 0 0 0
```

The high bits **agree**, so they survive. The lowest set bit is 1-and-0, so it
**dies**. Everything below is 0-and-1, so it **stays dead**.

**One set bit removed, everything else identical.**

## The popcount loop

```python
count = 0
while n:
    n &= n - 1        # clears the lowest set bit
    count += 1
return count
```

Step by step on 44, which has three set bits and therefore takes three passes:

```
   44    0 0 1 0 1 1 0 0     → count 1
   40    0 0 1 0 1 0 0 0     → count 2
   32    0 0 1 0 0 0 0 0     → count 3
    0    0 0 0 0 0 0 0 0     → loop ends
```

The three ways to popcount, in increasing order of cleverness — know all three
and present them in order, because the progression is what gets graded:

1. Test all 32 positions with `(n >> i) & 1`. **Always 32 steps.**
2. Shift right until zero, testing `n & 1` each pass. **O(log n)** steps —
   position of the highest set bit.
3. `n &= n - 1` in a loop. **O(popcount(n))** steps — and for sparse numbers
   that is much better than either of the above.

## The sibling identity

**`n & -n` *isolates* the lowest set bit** — gives a number with only that bit
on, rather than a number with it removed. Same borrow picture, seen through
[[twos-complement]], because `-n == ~n + 1`.

Pair them: `n & (n-1)` **clears**, `n & -n` **isolates**.

## Complexity

**O(popcount(n))** — one iteration per set bit, never worse than the shift loop
and often much better. **O(1) space.**

For a fixed-width input, say **"O(1), because the input is a fixed 32 bits"** —
with the reason attached, not just the claim.

## Gotchas

- **Precedence.** `n & (n - 1) == 0` parses as `n & ((n-1) == 0)`. Wrap the
  whole bitwise expression: `(n & (n - 1)) == 0`. Silently wrong, never raises.
- **`n = 0` in the power-of-two test.** `0 & -1 == 0` passes the identity but
  zero is not a power of two. The `n > 0` guard is not optional.
- **Negative `n` in Python.** `while n:` never terminates — it converges to `-1`,
  not `0`. Mask with `0xFFFFFFFF` first.
- **`n &= n - 1` vs `n & n - 1`.** The second works by precedence (`-` binds
  tighter than `&`) but reads as a puzzle. Parenthesise anyway.
- **No comment.** `n & (n - 1)` is unreadable to anyone who doesn't already know
  it. One line saying what it does earns more than the trick itself.
- Reaching for it when a hash map solves the problem readably and nobody asked
  for O(1) space. Mention it as the follow-up; implement the readable one.

## Related

- [[bitwise-operators]] — `&` as a masking operator, and the precedence trap
- [[twos-complement]] — why `n & -n` isolates rather than clears
- [[xor-cancellation]] — the topic's other carrying identity; that one folds a
  whole collection, this one dismantles a single number
- [[constraint-bounded-complexity]] — "O(1) because the width is fixed"

## Evidence

Problems where I used this unaided:
