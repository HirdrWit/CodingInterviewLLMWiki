---
type: concept
status: unseen
updated: 2026-09-14
sources: [wiki/lessons/18-bit-manipulation.md]
tags: [bit-manipulation]
---

# Bitwise Operators

**In one sentence:** An integer is a row of light switches, each worth `2^i`,
and `&` `|` `^` `~` operate on every column independently and simultaneously —
no carries, no interaction — which is exactly why they cost O(1).

§3.1–3.2 of [[../lessons/18-bit-manipulation|Lesson 18]].

## When to reach for it

Trigger: the problem talks about the *representation* rather than the value —
"bits", "binary", "without using `+`", "O(1) space", "flags", "mask", "power of
two", "subset of at most 32 elements".

Every other topic treats an integer as an atom: something you compare, add,
index with, hash. This is the one place you open the box. The fluency to aim for
is being able to look at an expression and **see the bits move**.

## The picture to keep

```
bit index:   7    6    5    4    3    2    1    0
           ┌────┬────┬────┬────┬────┬────┬────┬────┐
switches:  │ 0  │ 0  │ 0  │ 1  │ 0  │ 1  │ 1  │ 0  │
           └────┴────┴────┴────┴────┴────┴────┴────┘
place:      128   64   32   16    8    4    2    1
                            ▲         ▲    ▲
                           16    +    4  + 2   =  22
```

**Bit 0 is the rightmost, worth 1. Bit `i` is worth `2^i`.** Off-by-ones in this
topic are almost always someone counting bits from the left.

## The six operators

**AND (`&`) — 1 only where *both* are 1.** Reads as "keep only what they agree
on". The **masking** operator.

```
   12   0 0 0 0 1 1 0 0
 & 10   0 0 0 0 1 0 1 0
 ----   ---------------
    8   0 0 0 0 1 0 0 0
```

**OR (`|`) — 1 where *either* is 1.** Reads as "merge". The **set-a-bit**
operator.

```
   12   0 0 0 0 1 1 0 0
 | 10   0 0 0 0 1 0 1 0
 ----   ---------------
   14   0 0 0 0 1 1 1 0
```

**XOR (`^`) — 1 where they *differ*.** Don't read it as "exclusive or" and stop
there; read it as a **difference detector**. Half the topic falls out of that
reading — see [[xor-cancellation]].

```
   12   0 0 0 0 1 1 0 0
 ^ 10   0 0 0 0 1 0 1 0
 ----   ---------------
    6   0 0 0 0 0 1 1 0
```

**NOT (`~`) — flip every switch.** Straightforward in a fixed 32-bit world; not
straightforward in Python, where the result runs off to the left forever. See
[[twos-complement]].

```
 ~ 12   ...1 1 1 1 0 0 1 1     = -13
```

**Left shift (`<<`) — slide left, fill with zeros.** `x << k == x * 2**k`,
because every switch moves to a place worth twice as much.

```
   5 << 2
    5   0 0 0 0 0 1 0 1
   20   0 0 0 1 0 1 0 0
```

**Right shift (`>>`) — slide right, drop what falls off the end.**
`x >> k == x // 2**k` for non-negative `x`. Note it **floors**: the bits that
fall off the right are simply lost, so it rounds down, always.

```
   20 >> 2
   20   0 0 0 1 0 1 0 0
    5   0 0 0 0 0 1 0 1
```

## Masking: read, set, clear, toggle bit `i`

A **mask** is a number built for the sole purpose of selecting columns. The mask
for bit `i` is `1 << i` — a single 1 at position `i`, zeros everywhere else.

```python
mask = 1 << i          # 0...010...0   with the 1 at position i

x & mask               # non-zero iff bit i of x is set   → read
(x >> i) & 1           # same test, gives exactly 0 or 1  → read, normalised
x | mask               # bit i forced on                  → set
x & ~mask              # bit i forced off                 → clear
x ^ mask               # bit i flipped                    → toggle
```

Memorise the **shapes**, not the lines: **`&` reads and clears, `|` sets, `^`
toggles.** The special case `i = 0` — `x & 1` — tests oddness, and comes up
constantly.

A wider mask selects a whole field: `0xFFFFFFFF` is 32 ones, and ANDing with it
keeps only the low 32 bits.

## Complexity

**O(1) per operation on a fixed-width integer.** The columns are independent, so
the hardware does all 32 at once. That independence *is* the reason these are
cheap, and it is the property worth naming out loud.

A loop over all bits of a 32-bit int is O(32) = **O(1) — but say why**. Claiming
O(1) without naming the fixed width sounds like luck rather than understanding.

## Gotchas

- **Operator precedence, the big one.** `&`, `|`, `^` bind *looser* than `==` in
  Python. `x & 1 == 0` parses as `x & (1 == 0)` → `x & False` → `0`. Silently
  wrong, never raises. **Parenthesise every bitwise sub-expression:**
  `(x & 1) == 0`.
- **Counting bits from the wrong end.** Bit 0 is the rightmost; reading a
  diagram left to right and indexing from there is the standard off-by-one.
- `~` in Python does not give you a 32-bit complement — see [[twos-complement]].
- `>>` floors rather than truncating toward zero, which matters for negatives.
- **Writing a bit trick with no comment.** One line saying *what* it does (not
  how) is the difference between "clever" and "unmaintainable" in an
  interviewer's notes.

## Python notes

```python
bin(13)            # '0b1101'  — strip the 2-char prefix
format(13, '032b') # fixed width, no prefix — keep this in your scratch file
int('1101', 2)     # 13
(13).bit_length()  # 4  — position of the highest set bit
(13).bit_count()   # 3  — popcount, 3.10+. Name it, then write the loop anyway
```

## Related

- [[xor-cancellation]] — `^` read as a cancellation device rather than an operator
- [[n-and-n-minus-1]] — the identity that makes `&` do real work
- [[twos-complement]] — where the minus sign lives, and why `~` misbehaves in Python
- [[in-place-marker]] — the same instinct in matrices: pack state into what you
  already have rather than allocating

## Evidence

Problems where I used this unaided:
