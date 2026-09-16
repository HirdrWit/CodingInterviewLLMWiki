---
type: meta
updated: 2026-09-16
sources: []
tags: [spine]
---

# Review Queue

Cold re-attempts. Solving something once proves very little; solving it again
two weeks later, unaided, is what `confident` means.

Default schedule after a clean solve: **+3d → +10d → +30d → +90d**.
A failed re-attempt resets to +3d and demotes the status one rung.

---

## Due now

| Item | Skill | Last attempt | Result | Due |
|---|---|---|---|---|
| — | — | — | — | — |

*Both items due 2026-09-16 were attempted and both failed on their pre-stated
pass conditions. Neither failure was in the code.*

## Scheduled

| Item | Skill | Last attempt | Result | Due |
|---|---|---|---|---|
| [[../problems/contains-duplicate\|Contains Duplicate]] | [[../concepts/seen-set\|Seen-Set]] | 2026-09-16 | **`fail`** — code perfect in 3.5 min; time complexity stated as "O(n) worst" | **2026-09-19** |
| [[../problems/valid-anagram\|Valid Anagram]] | [[../concepts/frequency-map\|Frequency Map]] | 2026-09-16 | **`fail`** — code cold in 5.9 min, O(1) space nailed; `Counter` never named | **2026-09-19** |
| [[../problems/two-sum\|Two Sum]] | [[../concepts/complement-lookup\|Complement Lookup]] | 2026-09-14 | `solved cold`, 11 min | **2026-09-17** |

**Pass condition for Contains Duplicate**, revised 2026-09-16. The code half is
**proven and retired** — `seen = set()`, explicit returns, membership checked
before insert, all reproduced cold in 3.5 min. The only thing still under test is
the spoken analysis:

1. Time stated as **O(n) average and O(n²) worst** — both halves, unprompted.
2. Space reasoned against the constraints (`k ≈ 2·10⁹` does not bind → O(n)).
3. **One rejected approach named with its reason** (0 of 3 boxes so far).

Correct code with an incomplete complexity sentence fails again. This is the
third debrief in a row carrying the same caveat.

## Retired
*Passed +90d cold. Genuinely known.*

| Item | Skill | Retired |
|---|---|---|
| — | — | — |

**Pass condition for Valid Anagram**, revised 2026-09-16. Code is proven —
deduplicated, correct, with an early exit. Only the talking is under test:

1. **`Counter` named**, whether or not it is the version written.
2. Time as a **single total**: O(n), worst case included, *because* the bounded
   alphabet caps collision scans at 26.
3. Space O(1), stated flat — no "closer to O(1) than O(n)".
4. **One rejected approach named with its reason.**

---

## Standing note, added 2026-09-16

Both problems now in the queue failed on **one spoken line each**, with perfect
code. The queue has stopped testing whether Rob can solve these and started
testing whether he can *present* them. That is the correct thing for it to be
testing — see [[gaps|gaps]], where every Serious row is a process row.
