---
type: curriculum
status: learning
updated: 2026-09-16
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
| 1 | Contains Duplicate | Easy | seen-set | 2 | `re-attempt failed` — code cold in 3.5 min, complexity sentence wrong | [[../problems/contains-duplicate\|write-up]] |
| 2 | Valid Anagram | Easy | frequency map | 2 | `re-attempt failed` — code cold in 5.9 min, `Counter` never named | [[../problems/valid-anagram\|write-up]] |
| 3 | Two Sum | Easy | complement lookup | 1 | `solved cold` | [[../problems/two-sum\|write-up]] |
| 4 | Group Anagrams | Medium | canonical key | 1 | `solved cold` | [[../problems/group-anagrams\|write-up]] |
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

**2026-09-14, after problem 3.** All three Easies solved cold. First box with no
correctness issue *and* the process followed — brute force narrated up front,
complexity volunteered with the average-case caveat.

- **Space is O(min(n, k)).** Valid Anagram's k=26 binds → O(1); Two Sum's
  k≈2·10⁹ doesn't → O(n). Same formula, and which side you're on is a fact about
  the constraints, not the data structure.
- **Check before inserting** in any as-you-scan map, or an element pairs with
  itself.
- **Naming is the insight.** `complement = target - num` explains; `val = ...`
  does not.
- Last process step still missing: **name the approach rejected, and why.**

The Mediums (4–8) start here. Expect unfinished boxes — that is the point of them.

**2026-09-16, re-attempt of problem 1.** First failed item in the queue, and it
is worth being precise about *what* failed. The code came back perfectly from
memory in 3.5 minutes — `seen = set()`, good names, check-before-insert. Both
fixes from three days earlier survived without prompting. **The algorithm and the
data structure are done being tested.**

What failed was the sentence around the code: "O(n) worst" for time, which is the
average case wearing the worst case's label. Same error as 2026-09-13, flagged at
every debrief since.

The pattern across four boxes is now hard to miss and is worth stating plainly:

> **Every error this vault has recorded is in the talking, not the coding.**
> Zero algorithmic errors in four boxes. Four debriefs of analysis and narration
> misses.

That is the [[../meta/current-plan|plan]]'s hypothesis — fluent in the ideas,
rusty at the performance — getting its first piece of *re-attempt* evidence
rather than first-attempt evidence. The Mediums (4–8) are still what confirms or
kills it, because all four data points so far are Easies.

Practical consequence: on the remaining problems, **the complexity sentence is
the deliverable, not the code.** Write the code, then say the full sentence —
average, worst, space against the constraints, and the approach rejected.

**2026-09-16, re-attempt of problem 2.** Second fail of the day, same shape as
the first: the code arrived cold and correct, the sentence around it fell short
of a pass condition written three days earlier. `Counter` was named in the
2026-09-13 write-up as a required fix and still was not said.

**The genuine progress** is in the analysis. Complexity was decomposed for the
first time — per-element cost separated from the loop, average/worst attached to
the hash operation rather than the algorithm — and O(1) space was volunteered
with the bounded-alphabet reason. That was the headline miss on 2026-09-13 and it
is now fixed.

**The pairing worth keeping from today.** Both Easies re-attempted in one
session, and together they make the point neither makes alone:

| | Contains Duplicate | Valid Anagram |
|---|---|---|
| Key space `k` | ≈ 2·10⁹ | 26 |
| Does it bind? | no | **yes** |
| Space | O(n) | O(1) |
| Time, worst case | **O(n²)** — collisions are unbounded | **O(n)** — a collision scan is over ≤26 entries |

Same structure, same formula `O(min(n, k))`, opposite answers — and the bounded
alphabet constrains **time as well as space**, which was the half not spotted.
See [[../concepts/constraint-bounded-complexity]].

**2026-09-16, problem 4 — first Medium, solved cold.** Correct on first write,
canonical key reached immediately, no wrong turns in the algorithm. The
hypothesis in [[../meta/current-plan|the plan]] — fluent in the ideas, rusty at
the performance — survives its first contact with a harder problem.

**But the pace data is new and worth having.** 29 min 42 s of a 30 min box,
against 4–7 minutes on the Easies. Finished, correct, and with no margin left.
The ceiling is above this problem; it is not far above it.

**The error was dimensional, not phrasing.** Complexity given as O(n log n) when
it is **O(n · k log k)** — `sorted` runs over a string of length `k`, not over
the array. Two parameters were flagged before the box and one came back. That is
the standard follow-up on this exact problem.

New recurring item: **the check-then-insert shape has now appeared in three
consecutive problems** (1, 2, 4) and `defaultdict` / `setdefault` has not been
named once. It is the only live drill that has never fired.
