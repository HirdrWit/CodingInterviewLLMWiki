---
type: concept
status: unseen
updated: 2026-09-10
sources: []
tags: [arrays-hashing]
---

# Canonical Key

**In one sentence:** When things should be grouped by an *equivalence* rather
than by equality, invent a transformation that maps every member of a group to
one identical key — then the grouping is just a hash map.

## When to reach for it

Trigger: "group the ones that are equivalent", where equivalent ≠ equal.
`"eat"` and `"tea"` are anagrams but not equal, so neither can be the key —
something derived from both has to be.

## How it works

Choosing the canonical form **is** the problem; the grouping afterwards is
bookkeeping. For anagrams, `sorted(word)` works (O(k log k) per word), and a
26-length character-count tuple works and is faster (O(k)).

## Related

- [[hash-map]] · [[frequency-map]]

## Evidence

*Not yet studied — comes up at problem 4 of
[[../curriculum/arrays-hashing|Topic 1]]. Fill this in during that debrief.*
