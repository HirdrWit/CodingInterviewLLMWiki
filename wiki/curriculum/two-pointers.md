---
type: curriculum
status: unseen
updated: 2026-09-14
sources: [https://neetcode.io/practice/practice/blind75]
tags: [curriculum, two-pointers]
---

# Topic 2 — Two Pointers

**Lesson:** [[../lessons/02-two-pointers|Lesson 2 — Two Pointers]]
**Skill:** [[../skills/algorithms|Algorithms]]

3 problems. Small topic, but not a light one — the Easy builds the loop shape and
the two Mediums are both heavily-asked interview staples. Three Sum in particular
is as close to a guaranteed question as anything in the Blind 75.

Worked in this order. The Easy establishes the converging loop and its boundary
conditions; problem 2 adds sorting and duplicate handling; problem 3 swaps the
"steer toward a target" move rule for a greedy discard rule.

| # | Problem | Difficulty | Pattern | Attempts | Status | Write-up |
|---|---|---|---|---|---|---|
| 1 | Valid Palindrome | Easy | converging pointers + character filtering | 0 | `unseen` | — |
| 2 | Three Sum | Medium | sort, then fix one + converging pair, with duplicate skipping | 0 | `unseen` | — |
| 3 | Container With Most Water | Medium | greedy converging pointers on a width/height trade | 0 | `unseen` | — |

The **Pattern** column is filled in here for tracking, but read it *after* a
timed attempt, never before. Knowing "this is a greedy discard problem" going in
removes the exact skill the interview tests: recognising it yourself.

## Topic done when

- [ ] All 3 solved at least once
- [ ] All 3 re-solved **cold** (no notes, no hints) on a later day
- [ ] Can state time and space complexity for each without thinking, including
      why the sort vanishes from the Three Sum bound
- [ ] Can give the O(n) argument for a converging loop — the gap shrinks every
      iteration and starts at n — unprompted
- [ ] Can justify the greedy discard in Container With Most Water as an exchange
      argument, not as "it works"
- [ ] Can state, without hesitating, when two pointers is the wrong tool and a
      [[../concepts/hash-map|hash map]] is right

## Notes

*Accumulates as problems get worked — recurring mistakes, insights that transfer.*
