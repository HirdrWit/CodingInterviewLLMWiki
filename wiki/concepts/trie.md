---
type: concept
status: unseen
updated: 2026-09-14
sources: [https://www.geeksforgeeks.org/trie-insert-and-search/]
tags: [tries, data-structures]
---

# Trie (Prefix Tree)

**In one sentence, in my own words** — a tree where each edge is a character, so
a word is a *path* rather than a stored value, and every node along that path
*is* a prefix — which makes "does anything start with this?" a walk instead of a
scan.

Patterns A and B in [[../lessons/10-tries|Lesson 10]].

## When to reach for it

Trigger words: **"prefix"**, "starts with", "autocomplete", "dictionary of
words", "`.` matches any letter", "find **all** the words from this list that
appear in …".

The deeper trigger: a brute force whose inner loop tests **many stored strings
against one query, character by character**. A [[hash-map|hash map]] collapses an
inner loop that searches for an *exact* match. When the inner loop is searching
by *prefix*, hashing can't help — it deliberately scatters `pre`, `press` and
`pretend` into unrelated buckets. The trie is what collapses that loop.

Also reach for it when you need to search a grid or text for **many target words
at once** — then the trie stops being a lookup table and becomes a pruner
(Pattern C).

## How it works

Root is the empty prefix. Every operation is the same walk, one step per
character, differing only in what happens at the end.

```
insert:      walk, creating missing children, then set is_end = True
search:      walk, fail on a missing child, then RETURN node.is_end
startsWith:  walk, fail on a missing child, then RETURN True
```

That is the whole data structure. `search` and `startsWith` differ by exactly
one line — factor the walk into a private helper returning the node or `None`,
and build both on it.

## Minimal example

```python
class Trie:
    def __init__(self):
        self.root = TrieNode()

    def insert(self, word: str) -> None:
        node = self.root
        for ch in word:
            if ch not in node.children:
                node.children[ch] = TrieNode()
            node = node.children[ch]
        node.is_end = True

    def _walk(self, prefix: str) -> TrieNode | None:
        node = self.root
        for ch in prefix:
            if ch not in node.children:
                return None
            node = node.children[ch]
        return node

    def search(self, word: str) -> bool:
        node = self._walk(word)
        return node is not None and node.is_end     # the flag is the difference

    def starts_with(self, prefix: str) -> bool:
        return self._walk(prefix) is not None
```

Holding `cat`, `car`, `card`:

```
root ── c ── a ─┬─ t ■
                └─ r ■ ── d ■
```

`car` is marked even though the path continues. See
[[prefix-tree-node|prefix-tree-node]] for why that flag is load-bearing.

## Complexity

`L` = length of the word/prefix. `n` = number of words. `T` = total characters.

- `insert`, `search`, `startsWith`: **O(L)** time, and — the point — **entirely
  independent of `n`**. Ten words or ten million, `startsWith("pre")` is three
  steps.
- Build from n words: O(T).
- Space: **O(T)** nodes worst case. Say "O(L), independent of n", never "O(1)".

Each step is a dict lookup, so it's O(1) *average* per step, inheriting the
[[hash-map|hash map]] caveat.

Compare for "does any stored word start with `p`?": hash set O(n × L), sorted
list + binary search O(L log n), trie **O(L)** — and only the trie also hands you
the whole subtree of matches.

## Gotchas

- **Memory: a trie usually costs *more bytes* than a set of the same words**,
  despite storing fewer characters — every node is a Python object wrapping a
  dict. Be precise about which quantity you're claiming to save.
- Forgetting `is_end` → `search("app")` returns `True` after inserting
  `"apple"`. The canonical bug.
- Copy-pasting `search` into `startsWith` and forgetting to change the return.
- `defaultdict(TrieNode)` makes insert short but **creates nodes on read**,
  silently corrupting `search` and `startsWith`.

## Related

- [[prefix-tree-node]] — the two-field node this is built from; that page owns
  the `is_end` argument, this one owns the operations
- [[hash-map]] — O(1) for *exact* keys, useless for prefixes, because hashing
  destroys exactly the key-neighbour relationship a trie preserves
- [[seen-set]] — the same "collapse a searching inner loop" instinct, one level
  simpler

## Evidence

Problems where I used this unaided:

*(none yet — [[../curriculum/tries|Topic 10]] not started)*
