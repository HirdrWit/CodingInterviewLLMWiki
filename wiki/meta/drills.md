---
type: meta
updated: 2026-09-16
sources: []
tags: [spine]
---

# Drills

**Analysis only. No coding.** One or two minutes at the top of a session.

Created 2026-09-16, splitting the analysis half out of
[[review-queue|the review queue]]. The reason: both Topic 1 Easies were solved
cold with perfect code and still failed their re-attempts, each on a single
spoken line. Re-solving the problem to test the sentence is the wrong instrument.

**Only things that have actually failed get a row here.** Nothing that keeps
passing stays on the list.

Reference material — to *read*, not to re-earn:
[[../../notes/Complexity - reference card|notes/Complexity — reference card]].

---

## Live

| # | Drill | Failed on | Streak | Retires at |
|---|---|---|---|---|
| D1 | **Name what you rejected.** Given a problem, name one approach not taken and why, in one sentence | 0 across 5 boxes; **first fire 2026-09-16** ([[../problems/group-anagrams\|Group Anagrams]]) | **1 / 2** | 2 consecutive |
| D2 | **The hash-op worst case.** Given a problem, is the worst case O(n) or O(n²)? Justify from the key space | [[../problems/contains-duplicate\|Contains Duplicate]] 2026-09-13, 2026-09-16; **passed 2026-09-16** on [[../problems/group-anagrams\|Group Anagrams]] | **1 / 3** | 3 consecutive |
| D3 | **Name the stdlib tool.** Given a hand-rolled snippet, name what replaces it | [[../problems/valid-anagram\|Valid Anagram]] 2026-09-16 (`Counter`); [[../problems/group-anagrams\|Group Anagrams]] 2026-09-16 (`defaultdict`) | 0 / 2 | 2 consecutive |

## Passing, not yet retired

| # | Drill | Streak | Note |
|---|---|---|---|
| D4 | **Space against the constraints**, volunteered | 2 / 2 → **retiring** | Nailed on 2026-09-16 with the bounded-alphabet reason. Hold one spot check, then drop |
| D5 | **Count the parameters before stating complexity.** When a problem has both a number of items and a size per item, name both | 0 / 2 | **New 2026-09-16.** [[../problems/group-anagrams\|Group Anagrams]] answered O(n log n); it is O(n · k log k). Substantive, not phrasing — this is the standard interview follow-up on that problem |

## Retired

| # | Drill | Retired |
|---|---|---|
| — | — | — |

---

## How a drill runs

Asked cold, in chat, about **any** problem — including ones never solved, since
the point is the reasoning, not the recall. No editor, no lookups.

Answer in one or two sentences. Right or wrong is recorded the same session; a
wrong answer resets the streak and the reference card gets re-read.

## Why the phrasing matters at all

Rob flagged on 2026-09-16 that the debrief was too harsh on semantics, and on an
Easy that is fair — the code shipped correctly both times. These are kept because
they are **scored behaviours in a real loop**, not because the phrasing is
interesting:

- Saying "O(n) worst" when you mean average invites the follow-up *"is it,
  though?"*, and the recovery costs more than the caveat would have.
- Never naming a rejected approach is the single biggest gap between how this
  reads and how a senior candidate sounds. Cheapest thing on the board.
- Not naming `Counter` reads as not knowing it exists.

They are drilled in **seconds**, not in six-minute boxes. That is the whole point
of the split.
