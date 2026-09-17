---
type: company
updated: 2026-09-16
sources:
  - sources/2026-09-16-zipline-interview-research.md
  - https://www.zipline.com/
tags: [company, active-pipeline, drones, logistics, africa]
---

# Zipline

**Stage:** Late-stage private — $7.6B valuation, Series H ~$800M total (Jan 2026,
Valor Equity Partners leading) · **Size:** ~1,545 employees (2026-01-31) ·
**Location / remote:** HQ South San Francisco; African operations centred on
**Kigali, Rwanda** and Ghana, with remote options on some engineering roles ·
**Applied:** interview scheduled — see [[#Pipeline]]

Autonomous drone delivery ("instant logistics"). 2 million cumulative deliveries,
the second million roughly four times faster than the first. Seven countries;
the African division covers **Rwanda, Ghana, Nigeria, Kenya and Côte d'Ivoire**.

> ⚠️ Everything below about the interview process is **secondhand** — Glassdoor,
> Blind and interview-guide aggregators, not Zipline. Treat it as a prior to test
> against the recruiter, not as fact. The stack section is firmer: it comes from
> Zipline's own job postings.
>
> ⚠️ **There are two companies called Zipline.** Yours is Glassdoor **E1394276**
> (Transportation & Logistics). **Retail Zipline** is E1732473 — a retail-comms
> SaaS whose public Rails take-home is the top hit for "zipline take home" and is
> a decoy. See [[zipline-take-home#⚠️ First there are two companies called Zipline|the take-home page]].

## Why them

The medicine-transport framing is not marketing. Zipline runs **Rwanda's national
blood delivery network** and ran Ghana's COVID-19 vaccine distribution. In
February 2026 Rwanda became the first country to commit to a nationwide
autonomous delivery network — a third hub in Karongi District, Africa's first
urban drone delivery network, and Zipline's first overseas AI/robotics R&D
facility. A $150M US State Department contract (Nov 2025) is funding a tripling
of the African network from **5,000 to 15,000 health facilities**.

The honest version of "why them" for an interview is the engineering consequence
of that, not the mission slogan: a distributed system where a dropped message is
a blood unit that does not arrive, running on infrastructure that cannot assume
reliable connectivity, scaling 3x in facility count. That is a real constraint
story, and it is the one their interviews reportedly probe.

## Stack

From Zipline's own postings — the Africa-facing team is **Nest Applications
Software** (fulfilment, maintenance and flight systems powering operations in
Africa):

| Layer | Tech |
|---|---|
| Backend | **Go**, Python |
| Frontend | React |
| Messaging | **Kafka** (event-driven architecture) |
| Data | **PostgreSQL** (+ PostGIS on the maps side) |
| Infra | AWS, Kubernetes |
| Onboard / edge | Python, C++ on constrained real-time Linux |
| ML / data platform | PyTorch, Spark, Airflow, EKS, some Rust |

**What Rob needs that he doesn't have.** Go is his strongest language — that
lines up unusually well. The gaps against a senior full-stack Nest role are:

- **React / any modern JS framework.** Not on record in [[../meta/skill-tree|the skill tree]].
  Named explicitly in the JD.
- **Kafka / event-driven architecture.** Named explicitly.
- **PostgreSQL modelling and performance tuning** — beyond "can write SQL".
- **Kubernetes and AWS in production.**
- **Distributed systems design** — the JD asks 8+ years of it, and the loop has a
  dedicated system design round.

These are [[../meta/skill-tree|skill-tree]] bands 2–4, currently parked behind
Blind 75. See [[#Gaps against this role]].

## Their process

Reported six-stage senior loop, ~27 days end to end. Glassdoor difficulty 2.9/5
for SWE, 3.3/5 for Senior SWE; experience rated positive by only 33% of 22
reviewers, so expect some roughness.

| Round | Format | Prep notes |
|---|---|---|
| 1. Recruiter call | Background, interest in Zipline, role basics | Have the "why this mission, concretely" answer ready — see [[#Why them]]. Ask here: which division, which team, is it Nest Applications, is travel/relocation to Kigali expected |
| 2. Hiring manager screen | Conversational, depth on past systems | Pick two systems Rob has actually operated and know their failure modes cold |
| 3. Take-home | Stated 2–4 h; competitive submissions reportedly 8–12 h. A real operational problem | **See [[zipline-take-home\|the take-home page]]** — reported tasks, evaluation criteria and a prep checklist |
| 4. Technical interview on the take-home | **~45-minute presentation to a panel** asking why and how | The round that finds out whether you authored your own submission. See [[zipline-take-home#Preparing for the 45-minute defence\|the defence prep]]. Also reported: **code comprehension** — read an existing function and extend it |
| 5. System design | Reported themes: a communication bus for sensor data; backend services for flight coordination and airspace routing; critical supply-chain management | The strongest known gap. See [[#Gaps against this role]] |
| 6. Product prioritisation | Given constraints, what ships first | Operational empathy — the JD's first responsibility is *user research into operational workflows*. Argue from the person in the fulfilment centre, not from the architecture |
| (Also reported) | Behavioural HR round; cross-functional interview with an electrical engineer | Behavioural leans on **"tell me your worsts"** — failures and what was learned. Prepare three, with the learning named |

**The single most repeated claim across sources:** Zipline does *not* run standard
big-tech algorithmic puzzles. The loop is practical — case studies, take-homes,
deep discussion of real systems. Worth verifying with the recruiter, because if
true it **changes the prep ordering** for this pipeline. See [[#Gaps against this role]].

## Reported question themes

Behavioural and values:
- Why Zipline / why this mission
- "Customer obsession" in engineering work
- Your *worsts* — a design or outcome you were unsatisfied with, and the lesson
- Conflict between team members; a difficult decision you had to make
- Working independently; comfort with travel

Technical, conversational:
- Biggest challenge in scaling a drone delivery service
- Process for approaching a new problem
- Favourite debugging tools
- Designing and testing software; Python experience

Domain-flavoured coding: candidates report coding for a real-life use case where
knowing how your language meets the domain matters — e.g. serial ports for
hardware-adjacent work.

## Gaps against this role

Ranked. Feeds [[../meta/gaps|gaps]].

1. **System design has no evidence at all.** There is a dedicated round and the
   themes are specific (event buses, routing, coordination services). Nothing in
   [[../meta/skill-tree|the skill tree]] covers it.
2. **Event-driven architecture / Kafka.** Named in the JD, and the natural design
   answer to "communication bus for sensor information".
3. **React.** Named in the JD for a full-stack role. Zero on record.
4. **PostgreSQL depth** — modelling and performance tuning, not query writing.
5. **Kubernetes / AWS in production.**
6. **DSA** — ranked *last for this company specifically*, on the reported claim
   that they skip algorithmic puzzles. Do not let this reordering leak into the
   general plan: DSA is still the gate on nearly every other pipeline, and
   [[../curriculum/index|the curriculum]] stays the default work.

**This is a live conflict with [[../meta/profile|the profile]]**, which names the
target as a Senior Engineer / SEM role at an S&P-listed company (Vivint-shaped),
Boston hybrid. Zipline's Africa division is a late-stage private drone company
with roles centred on Kigali. Rob's call to make, not the wiki's — but
[[../meta/current-plan|the plan]] cannot be ranked honestly against both. Flagged,
not resolved.

## Pipeline

| Date | Event | Notes |
|---|---|---|
| 2026-09-16 | Interview mentioned — African division, medicine transport | Round, role title, team and date all **unknown**. Fill these in; the prep depends entirely on them |

## Open questions for the recruiter

Cheap to ask, and each one changes the prep:

1. Which role and level, and is it the Nest Applications Software team?
2. Does the loop include an algorithmic coding round, or is it take-home plus
   system design as reported?
3. Is the take-home really time-boxed to two hours, and is there a language
   preference?
4. Is travel or relocation to Kigali expected, or is it remote?
5. Who is on the panel — is there a cross-functional round with hardware?

## Sub-pages

- [[zipline-take-home|The take-home, and how to prepare]] — the pivot of the loop

## Interviews

None yet. Post-mortems go in [[../interviews/index|Interviews]].

Back to [[index|Companies]].
