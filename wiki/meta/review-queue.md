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

*Empty. Both items due 2026-09-16 were attempted, solved cold, and had their
code retired — see the policy change at the foot of this page. The analysis they
failed on moved to [[drills|drills]].*

## Scheduled

| Item | Skill | Last attempt | Result | Due |
|---|---|---|---|---|
| [[../problems/two-sum\|Two Sum]] | [[../concepts/complement-lookup\|Complement Lookup]] | 2026-09-14 | `solved cold`, 11 min | **2026-09-17** |
| [[../problems/group-anagrams\|Group Anagrams]] | [[../concepts/canonical-key\|Canonical Key]] | 2026-09-16 | `solved cold`, 29 m 42 s — first Medium | **2026-09-19** |

## Retired
*Passed +90d cold. Genuinely known.*

| Item | Skill | Retired |
|---|---|---|
| [[../problems/contains-duplicate\|Contains Duplicate]] | [[../concepts/seen-set\|Seen-Set]] | 2026-09-16 — **code retired early.** Solved cold twice, 7 min then 3 min 32 s, second attempt byte-identical to the target solution. Nothing left to test |
| [[../problems/valid-anagram\|Valid Anagram]] | [[../concepts/frequency-map\|Frequency Map]] | 2026-09-16 — **code retired early.** Solved cold twice, 7 min then 5 min 53 s, correct and deduplicated both times |

---

**Pass condition for Group Anagrams:** solved cold again, `defaultdict` or
`setdefault` used or named, and complexity stated in **both** `n` and `k`.

---

## Standing note, added 2026-09-16

Both problems now in the queue failed on **one spoken line each**, with perfect
code. The queue has stopped testing whether Rob can solve these and started
testing whether he can *present* them. That is the correct thing for it to be
testing — see [[gaps|gaps]], where every Serious row is a process row.

---

## Policy change, 2026-09-16

**Code and analysis are queued separately from here.** Rob's call, and it is
right: re-solving a proven Easy every 3 days spends the box re-confirming what
two clean solves already established, and the full `+3d → +10d → +30d → +90d`
ladder was being applied to a skill that had stopped failing.

Both Topic 1 Easies failed their re-attempts on **one spoken line each** with
perfect code. Bundling those into a problem re-attempt meant paying six minutes
of coding to test ten seconds of talking.

So:

- **Code re-attempts** stay in this queue, and retire once solved cold twice
  with no correctness issue.
- **Analysis** moves to [[drills|drills]] — asked directly, no coding, a minute
  or two at the top of a session. The semantics themselves are filed as a
  reference card at [[../../notes/Complexity - reference card|notes/Complexity]]
  to be *read*, not re-earned.

A drill that keeps failing is a real signal. A drill that passes twice retires
the same way a problem does.
