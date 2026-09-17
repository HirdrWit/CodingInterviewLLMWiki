---
type: company
updated: 2026-09-16
sources:
  - sources/2026-09-16-zipline-interview-research.md
tags: [company, zipline, take-home, prep]
---

# Zipline — the take-home, and how to prepare

Detail page for [[zipline|Zipline]]. Split out because the take-home is the
**pivot of the whole loop**: it gates the onsite, and the round after it is a
defence of the code you wrote.

> ⚠️ Every claim about the task itself is **aggregator-sourced** — Dataford,
> Glassdoor (employer E1394276), Blind. Not from Zipline. Nobody has posted the
> actual prompt. Prepare for the *shape*, not for a specific problem.

## ⚠️ First: there are two companies called Zipline

This matters because the top search result for "zipline take home" is the wrong
one.

| | Drone delivery — **yours** | Retail Zipline |
|---|---|---|
| Glassdoor | **E1394276** · Transportation & Logistics · 257 reviews · 3.7★ | E1732473 · Information Technology · 27 reviews · 4.7★ |
| Take-home | Not public | **`github.com/retailzipline/zipline_take_home`** — extend a bare-bones email service, Rails 7 + Hotwire/Turbo/Stimulus |

That public Rails repo is **not your take-home** and preparing against it is
wasted time. Everything on this page and on [[zipline]] is drawn from E1394276.

## What is actually known

| | |
|---|---|
| **Stated time** | 2–4 hours |
| **Realistic time** | Dataford: 3–4 h for "half decent". Candidates report **8–12 h** to be competitive |
| **Task type** | A real operational problem, not a puzzle. Build something that works |
| **Language** | Candidate's choice, as far as anyone reports. **Write it in Go** |
| **Follow-up** | A **~45-minute presentation to a panel** that asks why and how |

### Reported example tasks

Two specific shapes come up repeatedly. Both are recognisably Zipline's actual
business, which is why they're plausible:

1. **An inventory management and packing API** — distribute packages optimally
   under strict payload weight thresholds. (This is literally what a Zipline nest
   does: a fixed-capacity drone, a queue of orders, a weight ceiling.)
2. **A path-planning simulation** — compute risk-aware flight trajectories around
   localised weather anomalies.

Note what both are: **a constrained optimisation over a small domain model, with
an obvious greedy answer and a more interesting correct one.** That is the shape
to rehearse.

### Stated evaluation criteria

1. **Code quality and modularity** — extensible, self-documenting, organised
2. **Optimisation and trade-offs** — complexity understood, choices *justified*
3. **Error handling and edge cases** — graceful on unexpected input

Nothing in that list is "found the optimal algorithm". Two of the three are
about judgment and communication.

## How to prepare

### The honest risk, first

Rob's top Serious gap in [[../meta/gaps|gaps]] is *coding fluency from an empty
file, unaided* — rusty after four years of management and after AI tooling took
over the generative work. A take-home is the one format where that gap can be
papered over, and **the round immediately after it is designed to find out**.
A 45-minute panel asking "why did you do it this way" will expose code you did
not fully author, and it will expose it in front of five people.

So the rule for this take-home is: **whatever you use to write it, you must be
able to defend every line without notes.** If you cannot explain why a function
is shaped the way it is, delete it and write it again yourself. This is not a
purity argument — it is the actual scoring mechanism of round 4.

### The time decision

The 2–4 hour guideline is a lie by omission; the competitive submissions take
8–12. You have three options and should pick deliberately rather than drift:

- **Box it at 4 hours and say so in the README.** Defensible, senior, and it
  turns the constraint into a trade-offs conversation you control. Risky only if
  reviewers silently compare against 12-hour submissions — and they do.
- **Spend 8–12 hours.** Matches the competitive bar. Costs most of a weekend.
- **Box the code at ~5 hours, then spend 2 more on the README and tests.** The
  best ratio, because criteria 1 and 3 are cheaper to satisfy in prose and tests
  than in more features.

Recommendation: **the third.** Feature completeness is the least-weighted thing
on their stated list.

### What to actually rehearse

You cannot rehearse the prompt. You can rehearse the *delivery shape*, and that
is most of the score. Do one dry run against a self-set problem of the same
shape before the real one arrives — the inventory-packing one is the obvious
candidate, since it's both a reported example and a real Zipline problem.

A checklist for the submission:

- [ ] **A README that leads with the trade-offs**, not with setup instructions.
      Three sections: what I built, what I deliberately did not build and why,
      what I would do next with more time. This single file does more for
      criteria 1 and 2 than any amount of code.
- [ ] **Real tests, including the edge cases.** Criterion 3 is free marks and is
      the one most candidates skip under time pressure. An item heavier than the
      payload ceiling. An empty order list. A tie in the packing heuristic.
- [ ] **A named domain model.** `Payload`, `Nest`, `Order` — not `Item`, `Thing`,
      `data`. Your debriefs have flagged loose naming twice; this is the same
      weakness in a format where you have time to fix it.
- [ ] **The rejected approach, written down.** Your longest-running open gap is
      *never names the approach rejected and why* — 0 across 4 boxes. A take-home
      README is the easiest possible place to finally do it, and it is exactly
      what criterion 2 asks for.
- [ ] **Complexity stated in the README**, average *and* worst. Also a standing
      gap, also free marks here.
- [ ] **Error handling on the boundaries**, not everywhere. Senior signal is
      knowing where it matters.
- [ ] **Go.** It is your strongest language and it is in their stack.

### Preparing for the 45-minute defence

Treat this as the real interview and the code as an exhibit. Before submitting,
write out:

1. **A 5-minute walkthrough.** Problem, model, approach, trade-offs, what's
   missing. Rehearse it aloud. Do not screen-share a file tree and narrate it.
2. **The three questions you would ask if you were reviewing it.** Have answers.
   One of them is always "why this data structure".
3. **What breaks at 100x.** They run a real distributed system moving blood; the
   scaling question is certain.
4. **What you would do with a week.** Not "more tests" — name a specific design
   change and why you did not make it in four hours.

### Where this connects

- The defence round rewards exactly the behaviours the [[../meta/drills|drills]]
  train: naming the rejected approach, stating complexity unprompted, naming the
  guarantee you lean on. Those drills are worth more for this pipeline than the
  next Blind 75 problem is.
- System design is still the [[../meta/gaps|blocking gap]] and the take-home does
  not close it. Round 5 is separate.

## Open questions for the recruiter

Add to the list on [[zipline]]:

6. What is the actual time expectation on the take-home, and is it enforced?
7. Is there a policy on AI assistance?
8. Is the follow-up a panel presentation, and how long?
9. Language preference, or free choice?

## A caveat worth knowing

Multiple candidates report completing the take-home and **never hearing back**,
describing it as disrespectful of their time. Glassdoor rates the overall
interview experience 33% positive. That is not a reason to skip it — it is a
reason to box the time deliberately rather than pour a weekend into a lottery
ticket.

Back to [[zipline|Zipline]] · [[index|Companies]].
