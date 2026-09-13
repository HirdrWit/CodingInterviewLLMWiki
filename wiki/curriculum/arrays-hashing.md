---
type: curriculum
status: learning
updated: 2026-09-13
sources: [https://neetcode.io/practice/practice/blind75]
tags: [curriculum, arrays-hashing]
---

# Topic 1 — Arrays & Hashing

**Lesson:** [[../lessons/01-arrays-hashing|Lesson 1 — Arrays & Hashing]]
**Skill:** [[../skills/data-structures|Data Structures]]

8 problems. Worked in this order — each one adds exactly one new idea to the
previous. Do not skip ahead to the Mediums; the two Easies are where the *habits*
get built, and habits are what survive interview pressure.

| # | Problem | Difficulty | Pattern | Attempts | Status | Write-up |
|---|---|---|---|---|---|---|
| 1 | Contains Duplicate | Easy | seen-set | 1 | `solved cold` | [[../problems/contains-duplicate\|write-up]] |
| 2 | Valid Anagram | Easy | frequency map | 1 | `solved cold` | [[../problems/valid-anagram\|write-up]] |
| 3 | Two Sum | Easy | complement lookup | 0 | `unseen` | — |
| 4 | Group Anagrams | Medium | canonical key | 0 | `unseen` | — |
| 5 | Top K Frequent Elements | Medium | frequency + bucket sort | 0 | `unseen` | — |
| 6 | Encode and Decode Strings | Medium | length-prefix protocol | 0 | `unseen` | — |
| 7 | Product of Array Except Self | Medium | prefix/suffix accumulation | 0 | `unseen` | — |
| 8 | Longest Consecutive Sequence | Medium | set membership + sequence starts | 0 | `unseen` | — |

The **Pattern** column is filled in here for tracking, but read it *after* a
timed attempt, never before. Knowing "this is a complement lookup problem" going
in removes the exact skill the interview tests: recognising it yourself.

## Topic done when

- [ ] All 8 solved at least once
- [ ] All 8 re-solved **cold** (no notes, no hints) on a later day
- [ ] Can state time and space complexity for each without thinking
- [ ] Can explain to a rubber duck why a hash map is the right tool, and when it isn't

## Notes

*Accumulates as problems get worked — recurring mistakes, insights that transfer.*

**2026-09-13, after problem 1.** Two things to watch across the rest of the topic:

- **A dict whose values are never read is a set.** Inventing a placeholder value
  to satisfy a data structure means the structure is wrong. Watch for this again
  in problems 4 and 5, where the map genuinely *is* right — the contrast is the
  lesson.
- **"O(1)" about a hash operation always carries "on average".** Worst case is
  O(n) per lookup, so an O(n) scan is O(n²) worst case. Misstated at the first
  debrief; recheck at every debrief until it's automatic.
- **Finishing early is not the win condition.** 7 minutes used of a 30 minute
  box, 23 left on the table. Roll into the next problem.

**2026-09-13, after problem 2.** Both Easies now solved cold in 7 minutes each.
The algorithms are not the constraint — the analysis and the narration are.

- **Read the constraints into the complexity.** A bounded key space means O(1),
  not O(n). See [[../concepts/constraint-bounded-complexity]]. Recurs in
  problems 4 and 5.
- **Never write the same loop twice.** Extract it, or name the stdlib tool.
