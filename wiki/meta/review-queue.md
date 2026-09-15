---
type: meta
updated: 2026-09-14
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

## Scheduled

| Item | Skill | Last attempt | Result | Due |
|---|---|---|---|---|
| [[../problems/contains-duplicate\|Contains Duplicate]] | [[../concepts/seen-set\|Seen-Set]] | 2026-09-13 | `solved cold`, 7 min | **2026-09-16** |
| [[../problems/valid-anagram\|Valid Anagram]] | [[../concepts/frequency-map\|Frequency Map]] | 2026-09-13 | `solved cold`, 7 min | **2026-09-16** |
| [[../problems/two-sum\|Two Sum]] | [[../concepts/complement-lookup\|Complement Lookup]] | 2026-09-14 | `solved cold`, 11 min | **2026-09-17** |

**Pass condition for Contains Duplicate** is not just working code — it is
`seen = set()`, an explicit return on every path, and both complexities stated
correctly and unprompted. Working code with "O(n) worst case" is a fail.

## Retired
*Passed +90d cold. Genuinely known.*

| Item | Skill | Retired |
|---|---|---|
| — | — | — |
