---
type: note
updated: 2026-09-16
sources: [wiki/problems/contains-duplicate.md, wiki/problems/valid-anagram.md]
tags: [reference, complexity, arrays-hashing]
---

# Complexity — reference card

Filed 2026-09-16 at Rob's request. These are the **semantics** that kept getting
flagged at debriefs. They live here to be read, not to be re-earned by re-solving
Easies. Skim before a box; don't grind.

---

## The hash-op sentence

Any time you say O(1) about a dict or set operation:

> **O(1) on average.** Worst case a lookup degrades to a scan of the bucket.

Whether that matters for the *total* depends entirely on whether the key space is
bounded:

| | Key space `k` | Binds? | Space | Time, worst case |
|---|---|---|---|---|
| Contains Duplicate | ≈ 2·10⁹ | no | O(n) | **O(n²)** — collisions unbounded |
| Valid Anagram | 26 | **yes** | **O(1)** | **O(n)** — a bucket holds ≤26 |

Same structure, same formula, opposite answers. **The constraints decide, not the
data structure.**

The general form is `O(min(n, k))` for space. The part easy to miss: when `k`
binds, it caps **time** too, because a collision scan is over at most `k` entries.

## The four-part sentence

What a complete answer sounds like. Roughly ten seconds:

1. **Total time**, one expression — average and worst if they differ.
2. **Space**, stated flat. Not "closer to O(1) than O(n)". It either is or isn't.
3. **Why**, in terms of the constraints, not the data structure.
4. **What you rejected**, and the reason.

Worked example, Valid Anagram:

> "O(n) time — one pass per string, each character an average-O(1) dict op. The
> alphabet is bounded at 26, so a bucket can't hold more than that and the worst
> case stays O(n). Space is O(1) for the same reason. I rejected sorting both
> strings and comparing — that's O(n log n) for no space saving worth having."

Contains Duplicate:

> "O(n) average — n elements, each an expected-O(1) set lookup and insert. Worst
> case O(n²) if keys collide, though that takes adversarial input. Space O(n),
> since values run to ±10⁹ and the set can hold all n. I rejected the nested
> loop at O(n²), and sorting at O(n log n) — the extra space buys the better
> time bound and n is only 10⁵."

## Phrasing tics to drop

- "closer to O(1) than O(n)" → **"it's O(1)"**. Hedged complexity reads as
  uncertainty even when correct.
- Decomposing without totalling. Per-element cost is good reasoning, but finish:
  interviewers wait for one expression.
- Silence about the library. If it's `Counter`, say *"this is `Counter` — I'll
  write it out to show the mechanism."* Naming it and hand-rolling reads as
  fluent; hand-rolling silently reads as not knowing it exists.

## Stdlib worth naming out loud

| Instead of | Say |
|---|---|
| hand-rolled `char -> count` loop | `collections.Counter` |
| `if k not in d: d[k] = 0` | `d.get(k, 0) + 1`, or `defaultdict(int)` |
| a dict whose values are never read | that's a `set` |

---

Related: [[../wiki/concepts/constraint-bounded-complexity|Constraint-Bounded
Complexity]] · [[Lesson 1 - Arrays-Hashing|Lesson 1]]
