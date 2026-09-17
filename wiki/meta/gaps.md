---
type: meta
updated: 2026-09-16
sources: []
tags: [spine]
---

# Gaps

Ranked weaknesses. Drives what `plan` and the next `timed` problem pick.

Ranking is **severity against the target role in [[profile]]**, not difficulty.
Each gap: what's missing, how it was discovered, what would close it.

---

## Blocking
*Would sink an interview this week.*

| Gap | Discovered via | Closes when |
|---|---|---|
| **System design has zero evidence, and there is now a live pipeline with a dedicated design round.** [[../companies/zipline\|Zipline]]'s reported loop includes system design, with themes as specific as a sensor communication bus and backend services for flight coordination and airspace routing. `unseen` on the [[skill-tree\|skill tree]], no linked evidence | [[../companies/zipline\|Zipline]] research, 2026-09-16 | one design round rehearsed end to end and written up, with the tradeoffs named |
| **Cannot defend own code cold to a panel — untested.** [[../companies/zipline-take-home\|Zipline's take-home]] is followed by a ~45-minute panel presentation asking why and how. This is the round that intersects Rob's top Serious gap (coding fluency from an empty file, rusty since AI tooling took over generative work) | [[../companies/zipline-take-home\|Zipline take-home]] research, 2026-09-16 | one self-set take-home written and defended aloud, every line explained without notes |

*Severity depends on a date nobody has yet: the Zipline interview round and date
are unknown. If it is close, this is blocking. Ask, then re-rank.*

## Serious
*Will show up soon.*

| Gap | Discovered via | Closes when |
|---|---|---|
| **Coding fluency from an empty file, unaided and observed.** Rusty since moving to management and since AI tooling took over the generative work. Distinct from algorithm knowledge — 3 boxes produced zero algorithmic errors but recurring naming and structure weakness | self-reported 2026-09-14; **1 of 2** — [[../problems/group-anagrams\|Group Anagrams]] finished cold 2026-09-16 in 29 m 42 s of 30, correct, but names and shape still loose | 2 consecutive Mediums finished inside the box with clean naming, no assistance |
| Complexity analysis unproven — stating time *and* space unprompted is a scored behaviour, not a bonus | [[../lessons/01-arrays-hashing\|Lesson 1]], 2026-09-10 | 3 consecutive debriefs where complexity was stated correctly without being asked |
| **The average-case caveat on hash ops — back to 0 of 3.** Streak broken 2026-09-16: stated "O(n) worst", which is the average case labelled as the worst. Now flagged at 4 consecutive debriefs and the only gap that has survived a cold re-attempt of a problem whose *code* is fully proven. **Promoted to the top of Serious** — it is the single highest-frequency miss on record | missed [[../problems/contains-duplicate\|Contains Duplicate]] and [[../problems/valid-anagram\|Valid Anagram]] 2026-09-13; present [[../problems/two-sum\|Two Sum]] 2026-09-14; **missed again** [[../problems/contains-duplicate\|Contains Duplicate]] re-attempt 2026-09-16 | average *and* worst both stated unprompted in 3 consecutive debriefs |
| Constraints not read into the complexity — **2 of 2 → closing.** Volunteered unprompted on 2026-09-16: space given as O(n) with `k ≈ 2·10⁹` correctly not binding. Hold one more box to confirm | [[../problems/valid-anagram\|Valid Anagram]] 2026-09-13 (missed); [[../problems/two-sum\|Two Sum]] 2026-09-14 (correct, but prompted); **volunteered** [[../problems/contains-duplicate\|Contains Duplicate]] re-attempt 2026-09-16 | space reasoned against the constraints, unprompted, twice |
| Never names the approach rejected and why — **still 0, now across 4 boxes.** The cheapest of the process gaps to close and the most senior-sounding in a real loop, and it has never once been attempted | [[../problems/two-sum\|Two Sum]] 2026-09-14; [[../problems/contains-duplicate\|Contains Duplicate]] re-attempt 2026-09-16 | an alternative named and rejected with a reason, unprompted, in 2 consecutive boxes |

## Background
*Worth fixing, not urgent.*

| Gap | Discovered via | Closes when |
|---|---|---|
| Hours per week, target date and definition of done still blank in [[profile]], so weekly volume is a guess | wiki setup, 2026-09-10; partially filled 2026-09-14 | those three fields filled in |
| Behavioral and design claimed solid from 4 years of doing the EM job, but `unseen` on the [[skill-tree\|skill tree]] — no linked evidence. Doing the job well and performing it against a loop rubric are different skills | stated by Rob 2026-09-14 | one design post-mortem page written up from real Vivint work |
| **Zipline's named stack is unproven: React, Kafka/event-driven, PostgreSQL modelling and tuning, production Kubernetes/AWS.** Go is the one strong match. These are [[skill-tree\|skill-tree]] bands 2–4, deliberately parked — listed here so the parking is a decision and not an oversight | [[../companies/zipline\|Zipline]] job postings, 2026-09-16 | Rob decides whether the Zipline pipeline outranks [[profile\|the stated target]]; until then, parked |
| **[[profile\|The profile]] and the live pipeline disagree.** Profile targets Senior Engineer / SEM at an S&P-listed company, Boston hybrid. [[../companies/zipline\|Zipline]]'s African division is a late-stage private drone company centred on Kigali. Gap ranking is "severity against the target role" — with two target roles, the ranking is not honest | [[../companies/zipline\|Zipline]] research, 2026-09-16 | Rob updates [[profile]], or explicitly says Zipline is a one-off |
| Everything outside DSA is parked by choice, **reaffirmed 2026-09-14** after Rob corrected an initial suggestion to rebalance toward design/behavioral. DSA is the self-assessed weakest area and the perishable one | 2026-09-10; reaffirmed 2026-09-14 | Topic 1–3 are cold-solvable |

---

## How gaps get here

The honest ones come from **debriefs and real interviews**, not from
introspection. Every `debrief` that exposes a weakness adds a row, with the
problem write-up as the discovery source. That's the system working.

## Closed gaps

Kept, not deleted — the record of closing them is the record of progress.

| Gap | Closed | Evidence |
|---|---|---|
| **Narration under a timer** — brute force and complexity stated before the optimal solution, unprompted | 2026-09-16 | **3 consecutive boxes**, the third under Medium pressure: [[../problems/two-sum\|Two Sum]] 2026-09-14, [[../problems/contains-duplicate\|Contains Duplicate]] re-attempt 2026-09-16, [[../problems/group-anagrams\|Group Anagrams]] 2026-09-16 |
| DSA untested — no timed attempts on record, no idea where the real ceiling is | 2026-09-14 | All 3 Easies of [[../curriculum/arrays-hashing\|Topic 1]] attempted, debriefed and **solved cold**: [[../problems/contains-duplicate\|Contains Duplicate]] 7 min, [[../problems/valid-anagram\|Valid Anagram]] 7 min, [[../problems/two-sum\|Two Sum]] 11 min. The ceiling on Easies is not the binding constraint; the Mediums will locate the real one |
