---
type: meta
updated: 2026-09-14
sources: []
tags: [spine, scoreboard]
---

# Skill Tree

**Active focus: Band 1, via [[../curriculum/index|the Blind 75 curriculum]].**
Bands 2–4 are parked on purpose — see [[current-plan|current plan]].

The scoreboard. Every skill, its status, and the evidence that earned it.

Status ladder — see [[../../CLAUDE|CLAUDE.md]] for the full contract:
`unseen` → `learning` → `shaky` → `solid` → `confident`

**A status with no evidence link is a lint error.** Demotion after a failed
cold re-attempt is normal and expected.

---

## Band 1 — Fundamentals
*The floor. Nearly every technical screen tests these.*

| Skill | Status | Evidence | Notes |
|---|---|---|---|
| [[../skills/complexity\|Complexity analysis]] | `learning` | [[../lessons/01-arrays-hashing\|Lesson 1]] | Big-O, space, amortized. **Not promoted:** the caveat appeared 2026-09-14 after two misses, but the worst case is still unspoken and the constraint reasoning was prompted, not volunteered — see [[gaps]] |
| [[../skills/data-structures\|Data structures]] | `learning` | [[../lessons/01-arrays-hashing\|Lesson 1]], [[../problems/contains-duplicate\|Contains Duplicate]], [[../problems/valid-anagram\|Valid Anagram]], [[../problems/two-sum\|Two Sum]] | arrays, hashes, trees, heaps, graphs. [[../concepts/seen-set\|Seen-Set]], [[../concepts/frequency-map\|Frequency Map]] and [[../concepts/complement-lookup\|Complement Lookup]] are `solid` — all on Easies, all one solve each. Nothing beyond hashing tested |
| [[../skills/algorithms\|Algorithms]] | `learning` | [[../curriculum/index\|Blind 75]] | sorting, search, recursion, DP, greedy |

## Band 2 — Building
*What the job actually is day to day.*

| Skill | Status | Evidence | Notes |
|---|---|---|---|
| [[../skills/languages\|Language depth]] | `unseen` | — | one language, deeply |
| [[../skills/git\|Git & version control]] | `unseen` | — | branch, rebase, resolve, review |
| [[../skills/testing\|Testing]] | `unseen` | — | unit, integration, what's worth testing |
| [[../skills/databases\|Databases & SQL]] | `unseen` | — | joins, indexes, schema design, N+1 |
| [[../skills/web-fundamentals\|Web fundamentals]] | `unseen` | — | HTTP, REST, auth, browser model |
| [[../skills/tooling\|Tooling & environments]] | `unseen` | — | shell, debugger, CI, containers, deploy |

## Band 3 — Designing
*Separates a junior from someone worth hiring.*

| Skill | Status | Evidence | Notes |
|---|---|---|---|
| [[../skills/system-design\|System design]] | `unseen` | — | scaling, caching, queues, tradeoffs. **Claimed solid** from real work (2026-09-14); stays `unseen` until a design post-mortem links |
| [[../skills/concurrency\|Concurrency]] | `unseen` | — | async, threads, races, locks |
| [[../skills/code-quality\|Code quality & review]] | `unseen` | — | naming, decomposition, reading others' code |

## Band 4 — Getting hired
*Skills in their own right. Under-practiced by almost everyone.*

| Skill | Status | Evidence | Notes |
|---|---|---|---|
| [[../skills/behavioral\|Behavioral interviewing]] | `unseen` | — | STAR stories, failure stories. **Claimed solid** from 4 years as an EM (2026-09-14); stays `unseen` under the evidence rule until something links |
| [[../skills/communication\|Technical communication]] | `unseen` | — | thinking aloud, whiteboarding, writing. **Now the top DSA risk** — see [[gaps]]. Parked as a band, but narration is being trained inside every `timed` box |
| [[../skills/job-search\|Job search & applications]] | `unseen` | — | resume, outreach, pipeline, negotiation |

---

## Portfolio evidence

Skills claimed on a resume need something shipped behind them.
See [[../projects/index|Projects]] — currently empty.

| Project | Status | Skills it proves |
|---|---|---|
| — | — | — |

## Interview evidence

The realest signal available. See [[../interviews/index|Interviews]] — currently empty.

| Date | Company | Round | Outcome | Post-mortem |
|---|---|---|---|---|
| — | — | — | — | — |
