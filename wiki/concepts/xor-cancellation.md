---
type: concept
status: unseen
updated: 2026-09-14
sources: [wiki/lessons/18-bit-manipulation.md]
tags: [bit-manipulation]
---

# XOR Cancellation

**In one sentence:** XOR is its own inverse, so folding a whole pile of values
together with `^` annihilates everything that appears an even number of times
and leaves exactly what didn't pair up — an O(1)-space answer to questions that
otherwise want a hash set.

Pattern D and §3.3 of [[../lessons/18-bit-manipulation|Lesson 18]].

## When to reach for it

Trigger: "every element appears twice except one", "one value is missing", "find
the element that doesn't have a partner", "find the duplicate", **and** a space
constraint that rules out the obvious set.

The tell is **pairing**. If the structure of the problem is "things should match
up, one doesn't", XOR is the tool that makes the matched ones disappear for free.

## How it works

Three properties, and the third is the one to read carefully:

```
x ^ x == 0          anything XORed with itself vanishes
x ^ 0 == x          zero is the identity
a ^ b ^ a == b      order doesn't matter: XOR is commutative and associative
```

Because the order is free, you can XOR things together **in any sequence** and
every value appearing an even number of times cancels itself out of the result.
That turns XOR from an operator into a **cancellation device**.

Column by column it's obvious: `^` puts a 1 where the inputs **differ**, and a
value never differs from itself, so every column of `x ^ x` is 0.

```
   12   0 0 0 0 1 1 0 0
 ^ 12   0 0 0 0 1 1 0 0
 ----   ---------------
    0   0 0 0 0 0 0 0 0
```

The second-order consequence, which is the part people miss: because XOR is its
own inverse, you can **cancel a value back out** of an accumulated result later,
without knowing anything else in the pile. Nothing else in the topic has that
property.

## The extension that makes it a pattern

Beyond "fold one list", you can XOR together **two different piles** — the
values you *have* and the values you *expected* — and everything present in both
cancels, leaving exactly the discrepancy.

That works even when the two piles are generated completely differently: one
from an array, one from a range of indices. The piles don't need to be the same
shape, the same order, or built the same way. They only need to overlap.

```python
result = 0
for x in one_pile:
    result ^= x
for x in other_pile:
    result ^= x
# result == whatever failed to appear in both
```

## Minimal example

```python
a, b, c = 4, 7, 4
a ^ b ^ c          # 7   — the 4s cancel, order irrelevant
c ^ a ^ b          # 7   — same
```

## Complexity

**O(n) time, O(1) space.**

One pass, one accumulator. The O(1) space is the entire selling point — the
obvious [[seen-set|hash-set answer]] from [[../lessons/01-arrays-hashing|Lesson 1]]
is the same O(n) time at O(n) space, which is why this only earns its keep when
the space constraint is stated.

## Know the non-bitwise route too

For "find the missing number in `0..n`" there is a second, completely
non-bitwise answer: sum the range with Gauss's formula `n(n+1)/2` and subtract
the actual sum.

Know both. The sum version is more obvious and risks **overflow** in fixed-width
languages; the XOR version **cannot overflow**, because it never produces a value
larger than its inputs. **Being able to offer both, and say why you'd pick one,
is worth more than either alone.**

## Gotchas

- **Precedence.** `^` binds looser than `==`. `a ^ b == 0` is not what you meant.
  Parenthesise.
- **It only works for even/odd pairing.** Three copies of a value do *not*
  cancel — the technique answers "which appears an odd number of times", and
  problems promising "exactly twice" are a special case of that. If the promise
  is "three times", this is the wrong tool.
- **Initialising the accumulator to something other than 0.** `0` is the
  identity; anything else silently contaminates the fold.
- **It finds the unpaired value, not its index.** If the problem wants a
  position, XOR alone is not the answer.
- Reaching for it when the interviewer hasn't asked for constant space. Say the
  hash-set solution, then offer this.

## Related

- [[bitwise-operators]] — `^` read as a difference detector, which is where all
  three properties come from
- [[n-and-n-minus-1]] — the topic's other carrying identity; that one dismantles
  a single number, this one folds a whole collection
- [[seen-set]] — the O(n)-space solution this replaces, and the honest first
  answer to give
- [[frequency-map]] — what you need instead when the pairing isn't even/odd
- [[twos-complement]] — `x ^ MASK` used as a bulk bit-flip rather than a
  cancellation
- [[in-place-marker]] — the other route to "O(1) space" when the obvious
  solution allocates

## Evidence

Problems where I used this unaided:
