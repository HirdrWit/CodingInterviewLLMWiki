---
type: meta
updated: 2026-09-14
sources: []
tags: [spine, resources]
---

# Learning Resources

Curated per topic. All links verified reachable on 2026-09-10.
Quality over volume — three good sources read properly beat twenty bookmarked.

---

## Primary track

- **NeetCode Blind 75** — https://neetcode.io/practice/practice/blind75
  The problem list itself. Every problem has a free video walkthrough; watch it
  only *after* a timed attempt and a [[../../CLAUDE|debrief]].
- **NeetCode on YouTube** — https://www.youtube.com/@NeetCode
  Search the problem name. Clear, short, always explains the brute force first.

## LeetCode study plans

- **All study plans** — https://leetcode.com/studyplan/
- **LeetCode 75** — https://leetcode.com/studyplan/leetcode-75/
  A gentler parallel track. Good source of *extra reps* on a topic that hasn't
  stuck — same patterns, different problems.
- **Top Interview 150** — https://leetcode.com/studyplan/top-interview-150/
  The follow-on after Blind 75. Not yet.

*(These return 403 to command-line tools — that's LeetCode's bot filter, not a
dead link. They open normally in a browser.)*

## Topic 1 — Arrays & Hashing

**Arrays**
- Array data structure — https://www.geeksforgeeks.org/array-data-structure/

**Hashing** — read in this order:
- Introduction to hashing — https://www.geeksforgeeks.org/introduction-to-hashing-2/
- Hashing data structure (hub) — https://www.geeksforgeeks.org/hashing-data-structure/
- Separate chaining — https://www.geeksforgeeks.org/separate-chaining-collision-handling-technique-in-hashing/
- Open addressing — https://www.geeksforgeeks.org/open-addressing-collision-handling-technique-in-hashing/
- Load factor & rehashing — https://www.geeksforgeeks.org/load-factor-and-rehashing/
  *This is the page that explains why "O(1) average" is a real claim and not a lie.*

**Prefix technique** (needed for problem 7)
- Prefix sum array — https://www.geeksforgeeks.org/prefix-sum-array-implementation-applications-competitive-programming/

**Python specifics**
- Dictionaries — https://www.geeksforgeeks.org/python-dictionary/
- `collections.Counter` — https://www.geeksforgeeks.org/counters-in-python-set-1/
- **Complexity cheat sheet for Python operations** —
  https://www.geeksforgeeks.org/complexity-cheat-sheet-for-python-operations/
  *Print this. Half of all accidental O(n²) solutions come from not knowing
  which built-in is secretly a loop.*

**Video**
- Computerphile, *Hashing Algorithms and Security* — search YouTube; good for
  intuition on what a hash function is actually doing.
- NeetCode's Arrays & Hashing playlist — one video per problem above.

## Topics 2–18

Per-topic reading lives in **section 9 of each lesson** — see
[[../lessons/index|the lesson index]]. Each lesson lists its own materials in the
order to read them, with a note on what each one is actually good for, rather
than duplicating a flat list here.

The two links at the top of this page (NeetCode Blind 75, NeetCode on YouTube)
cover every topic. The rule holds throughout: **watch the video only after a
timed attempt and a debrief.**

This section stays thin on purpose. Add a link here only when a source proves
useful across more than one topic; single-topic sources belong in that lesson.

## How to add to this page

Say `ingest <url>` — the raw copy lands in `sources/`, the distilled version
goes into the relevant wiki page, and the link is added here with a note on what
it was actually good for.
