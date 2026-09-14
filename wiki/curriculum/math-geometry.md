---
type: curriculum
status: unseen
updated: 2026-09-14
sources: [https://neetcode.io/practice/practice/blind75]
tags: [curriculum, math-geometry]
---

# Topic 17 — Math & Geometry

**Lesson:** [[../lessons/17-math-geometry|Lesson 17 — Math & Geometry]]
**Skill:** [[../skills/algorithms|Algorithms]]

3 problems, all Mediums, all matrix manipulation. No new data structure and no
new complexity idea — the entire topic is **index discipline under pressure**.
Every one of these is easy to describe and easy to get subtly wrong, which is
precisely why interviewers like them.

Work them in this order. Problem 1 teaches composing two simple transforms,
problem 2 teaches boundary variables, problem 3 takes away your extra space and
makes you hide state in the input.

| # | Problem | Difficulty | Pattern | Attempts | Status | Write-up |
|---|---|---|---|---|---|---|
| 1 | Rotate Image | Medium | in-place transpose, then reverse each row | 0 | `unseen` | — |
| 2 | Spiral Matrix | Medium | four shrinking boundaries | 0 | `unseen` | — |
| 3 | Set Matrix Zeroes | Medium | first row and column as marker storage | 0 | `unseen` | — |

The **Pattern** column is filled in here for tracking, but read it *after* a
timed attempt, never before. Knowing "this is a boundary-variable problem" going
in removes the exact skill the interview tests: recognising it yourself.

## Concepts

- [[../concepts/matrix-in-place-transform|Matrix In-Place Transform]] — problem 1
- [[../concepts/layer-by-layer-traversal|Layer-by-Layer Traversal]] — problem 2
- [[../concepts/in-place-marker|In-Place Marker]] — problem 3

## Topic done when

- [ ] All 3 solved at least once
- [ ] All 3 re-solved **cold** (no notes, no hints) on a later day
- [ ] Can state time and space complexity for each without thinking, and point at
      the line that justifies the space claim
- [ ] Can derive all four rotations (90° CW, 90° CCW, 180°) from transpose and
      reversal, without looking them up
- [ ] Can write the spiral guards correctly on a 1×4 and a 3×3, first try
- [ ] Can explain why the marking pass and the applying pass must be separate

## Notes

*Accumulates as problems get worked — recurring mistakes, insights that transfer.*
