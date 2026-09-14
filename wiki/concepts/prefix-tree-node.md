---
type: concept
status: unseen
updated: 2026-09-14
sources: [https://www.geeksforgeeks.org/trie-insert-and-search/]
tags: [tries, data-structures]
---

# Prefix Tree Node

**In one sentence, in my own words** — the two-field building block of a
[[trie|trie]]: a dict of child nodes keyed by the next character, plus a boolean
saying "a complete word ends here" — and the boolean is not optional, because
the tree encodes *paths* but nothing about *which paths are words*.

Section 3 of [[../lessons/10-tries|Lesson 10]].

## When to reach for it

Any time you're about to implement a trie. It's a design decision, not an
algorithm, so the trigger is "I need a trie" plus "an interviewer is reading
this" — these show up in the Blind 75's only real
[[../skills/code-quality|design-a-data-structure]] problems.

## How it works

```python
class TrieNode:
    def __init__(self):
        self.children: dict[str, "TrieNode"] = {}
        self.is_end: bool = False
```

**The node stores no character of its own.** The character is the *key in the
parent's dict* — a label on the edge, not on the node. Duplicating it inside the
node is redundant state you then have to keep in sync.

### Why `is_end` is load-bearing

Insert only `"card"`, then ask whether `"car"` is stored:

```
root ── c ── a ── r ── d ■
                  ↑
            walk succeeds here — every letter exists —
            but "car" was never inserted. Answer must be False.
```

The walk cannot tell you. "Has children" doesn't mean "not a word" (`car` is
both a word and a prefix of `card`), and "is a leaf" isn't a maintainable proxy
for "is a word" either. The **only** thing separating *I reached this node* from
*a word ends at this node* is the flag.

Omitting it is the single most common first-trie bug, and it's the first thing
the test cases check.

### `dict` vs a 26-slot array

| | `dict[str, TrieNode]` | `[None] * 26`, indexed `ord(c) - ord('a')` |
|---|---|---|
| Per-step cost | a hash | an array index — faster, cache-friendly |
| Space per node | only the children that exist | 26 slots regardless |
| Alphabet | any — digits, unicode, whatever | hard-coded lowercase a–z |
| Reads as | obvious | needs a comment |

**Use the dict in interviews.** Shorter, survives "what about digits?", and the
constant factor isn't what's being graded. Know the array version exists and why
— that's the follow-up question.

## Minimal example

Get-or-create and step, the operation every insert is made of:

```python
if ch not in node.children:
    node.children[ch] = TrieNode()
node = node.children[ch]
```

`__slots__ = ("children", "is_end")` is a real per-node memory win if asked to
optimise, and costs nothing to add.

## Complexity

One node is O(1) to create. A node's dict lookup is O(1) *average* — see
[[hash-map|hash map]]; a trie step is a hash, not free.

Across a whole trie: **O(T) nodes** for `T` total characters, worst case. And
each node is a full Python object wrapping a dict — order of a hundred-plus
bytes — so the trie is typically **heavier in bytes** than a set holding the
same words, even while storing fewer characters.

## Gotchas

- Omitting `is_end`. See above.
- Storing the character on the node as well as using it as the parent's key —
  two sources of truth.
- `defaultdict(TrieNode)` for `children`: any *read* of a missing key creates a
  node, which silently breaks `search` and `startsWith` and quietly grows the
  trie. Use `.get(ch)` on the read paths, or just the explicit two-liner.
- Raw nested dicts with a `{"*": True}` end marker. It works, it's shorter, and
  it conflates data with structure — an interviewer reads it as not knowing the
  difference.

## Related

- [[trie]] — the structure built from these; that page owns the operations and
  their costs, this one owns the node's shape and the `is_end` argument
- [[hash-map]] — what `children` actually is, and where the per-step cost comes
  from

## Evidence

Problems where I used this unaided:

*(none yet — [[../curriculum/tries|Topic 10]] not started)*
