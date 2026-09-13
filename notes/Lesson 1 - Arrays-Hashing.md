
|                                | Trigger in the statement                                                    | Tool it summons                                                                                                                                    |
| ------------------------------ | --------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| A — seen-set                   | "contains a duplicate", "have we visited", "is it unique"                   | Set of what you've passed; ask before processing. O(n²) → O(n).                                                                                    |
| B — frequency map              | "anagram", "how many times", "most common"                                  | `value → count`. Anagrams ⟺ equal frequency maps.                                                                                                  |
| C — complement lookup          | "two numbers summing to target", "find a pair such that…"                   | Don't search for _pairs_ — search for the _partner_. At `x`, you need exactly `t - x`. Build the map **as you scan**, not before.                  |
| D — canonical key              | "group the equivalent ones", where equivalent ≠ equal                       | Invent a transform mapping every group member to an identical key (sorting chars: `"eat"`, `"tea"` → `"aet"`). Choosing the form _is_ the problem. |
| E — prefix/suffix              | "for every position, something about everything _else_", "without division" | Sweep left accumulating what's before, sweep right accumulating what's after, combine. Two passes, O(n).                                           |
| F — set as O(1) neighbour test | "longest consecutive run", unordered, sorting too slow                      | Set makes "does `x+1` exist?" O(1). Only start a walk where `x-1` is absent — that's what keeps it O(n).                                           |

---

# Reminders

Built from what actually went wrong in the boxes on 2026-09-13. The top section
is the one that matters — nothing below it has cost me anything yet.

## The ritual — before writing a line

Both Easies were solved cold in 7 minutes. **Neither was narrated.** Silent
correct code and a lucky guess look identical from the other side of the table.

1. **Restate the problem** in one sentence. If I can't, I haven't read it.
2. **Type the brute force as a comment** — the approach and its complexity.
   Sixty seconds. Delete it at the end.
3. **Read the constraints out loud.** They are part of the problem, not decoration.
4. *Then* write the real solution.
5. **State time and space, unprompted**, before being asked.
6. **Name the approach I rejected and why** ("sorting gets there in O(n log n),
   but…"). Cheapest way to sound senior.

> The brute force is often *harder* to get right than the optimal solution.
> Writing it out surfaces the subtleties early — e.g. "every char of s appears
> in t" tests the same **set** of letters, not the same **multiset**.

## Complexity — say it this way, every time

- **"O(1) *on average*"** for any hash operation. The words come in the same
  breath. Missed twice in one day.
- Worst case for a hash op is **O(n)** (all keys collide) → an O(n) scan of hash
  lookups is **O(n²) worst case**. Live in Python: `hash(x) == x` for ints.
- **Bounded key space → O(1) space, not O(n).** "Lowercase English letters" = 26
  possible keys = the map never grows with n. Say it with the reason:
  *"O(k) where k is the alphabet size, bounded at 26, so O(1)."*
  - Follow-up to expect: *"what if it were Unicode?"* → k unbounded, now
    honestly O(min(n, k)). A hash map survives that; a fixed 26-slot array doesn't.
- Say the **space cost** of any structure built over the input. Don't wait to be asked.
- Watch for costs assumed to be free: `dict_a == dict_b` is O(k), not atomic.
- `sorted()` is O(n log n). Fine if intended, fatal if I thought I wrote O(n).

## Picking the structure

- **A dict whose values I never read is a set.** If I'm inventing a placeholder
  (`d[x] = 1`, `d[x] = True`) to satisfy the structure, the structure is wrong.
- Set = *whether* I've seen it. Map = *what* I saw with it.
- Same machine, same O(1) average — the choice is a **statement to the reader**
  about what I understood the problem to need.
- Sorted input → two pointers, O(1) space. Unsorted → hash map, O(n) space.
  This fork comes back constantly.

## Code an interviewer is reading

- **Never write the same loop twice.** Extract it the moment I'm about to paste.
- Name things for what they *are*: `seen`, `counts` — not `result`, not `dict_s`
  (that names the type, not the meaning).
- **Explicit return on every path.** Falling off the end returns `None`.
- Cheap guards first: `if len(s) != len(t): return False` costs O(1) and rejects
  a whole class of input before allocating anything.
- Know the stdlib **and** the mechanism: reach for `Counter`, then say *"that's
  doing what I'd hand-roll — want me to write it out?"* One-liner alone reads as
  memorised; hand-rolling when a one-liner exists reads as not knowing my tools.

## Python traps

```python
if x in my_list:   # O(n)  — a hidden loop
if x in my_set:    # O(1)  — one hash
```
Identical syntax, different cost. The most common accidental O(n²).

- `counts.most_common(k)` **sorts** → O(n log n), not O(n).
- Keys must be immutable — `tuple(my_list)` to make a list hashable.
- `freq[ch] = freq.get(ch, 0) + 1` beats the `if/continue` dance.
  `defaultdict(int)` and `Counter` beat both.
- `enumerate(nums)`, never `range(len(nums))`.

## Edge cases to check before saying "done"

`[]` · `[x]` · all elements identical · `""` and `[""]` · negatives ·
input at the constraint maximum. Say them out loud **even when the constraints
rule them out** — that's the part that gets noticed.

## The one idea under all of it

**Spend memory, buy time.** Whenever a brute force has an inner loop whose only
job is *searching*, ask whether a hash structure answers that search in O(1).
That single question solves most of Arrays & Hashing.
