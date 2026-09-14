---
type: curriculum
status: unseen
updated: 2026-09-14
sources: [https://neetcode.io/practice/practice/blind75]
tags: [curriculum, tries]
---

# Topic 10 — Tries

**Lesson:** [[../lessons/10-tries|Lesson 10 — Tries]]
**Skill:** [[../skills/data-structures|Data Structures]] · [[../skills/code-quality|Code Quality]]

3 problems, and they are strictly cumulative — problem 2 is problem 1 with one
method rewritten, and problem 3 is problem 1 bolted onto
[[backtracking|Topic 9]]. Work them in order; skipping to the Hard without the
trie in your fingers wastes the box.

Note that 1 and 2 are **design** problems: the class structure is graded, not
just the return values. See [[../skills/code-quality|Code Quality]].

| # | Problem | Difficulty | Pattern | Attempts | Status | Write-up |
|---|---|---|---|---|---|---|
| 1 | Implement Trie (Prefix Tree) | Medium | trie construction: insert / search / startsWith | 0 | `unseen` | — |
| 2 | Design Add and Search Words Data Structure | Medium | trie + wildcard DFS | 0 | `unseen` | — |
| 3 | Word Search II | Hard | trie as pruner + grid backtracking | 0 | `unseen` | — |

The **Pattern** column is filled in here for tracking, but read it *after* a
timed attempt, never before. Knowing "this is a pruning problem" going in removes
the exact skill the interview tests: recognising it yourself.

## Topic done when

- [ ] All 3 solved at least once
- [ ] All 3 re-solved **cold** (no notes, no hints) on a later day
- [ ] Can write `TrieNode` and `Trie` from memory in under 10 minutes, with the
      walk factored out once rather than duplicated
- [ ] Can state why `is_end` is necessary, with a concrete two-word example
- [ ] Can state the complexity of each operation as *O(L), independent of n* —
      and give the honest space cost, not the "tries save memory" version
- [ ] Can explain to a rubber duck what the trie prunes in Word Search II, and
      why a hash set cannot do the same job

## Notes

*Accumulates as problems get worked — recurring mistakes, insights that transfer.*
