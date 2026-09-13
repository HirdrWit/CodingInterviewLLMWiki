---
type: concept
status: learning
updated: 2026-09-13
sources: [https://www.geeksforgeeks.org/introduction-to-hashing-2/]
tags: [arrays-hashing, core]
---

# Hash Map

**In one sentence:** A structure that *computes* where a key's value lives
instead of searching for it, turning "is this in here?" from O(n) into O(1).

## When to reach for it

- A brute-force solution has a nested loop whose inner job is **searching**
- You need to remember what you've already seen while scanning once
- You need to associate a value with a key and look it up repeatedly

Not when: you need sorted order, or the problem demands O(1) space.

## How it works

`hash(key) % num_buckets` → a bucket index. Same key always hashes the same, so
lookup goes straight there. Collisions are resolved by chaining or probing.
When `entries / buckets` (the **load factor**) crosses ~0.66, the table grows
and rehashes — O(n) that amortizes to O(1) per insert.

## Complexity

O(1) average for insert, lookup, delete. **O(n) worst case** if keys collide
pathologically. O(n) space, ~2–3× the raw data. Say the caveat out loud.

## Gotchas

- Keys must be immutable — `tuple(a_list)` to use a list as a key
- `x in a_list` is O(n); `x in a_set` is O(1). Same syntax, different world
- No inherent order — sorting the keys costs O(n log n)

## Related

- [[frequency-map]] — a hash map specialised to counting
- [[complement-lookup]] — a hash map used to find pairs in one pass
- [[two-pointers]] — the O(1)-space alternative, but only on sorted input

## Evidence

*None yet.*
