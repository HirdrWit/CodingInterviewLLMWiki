---
type: concept
status: unseen
updated: 2026-09-14
sources: [https://www.geeksforgeeks.org/greedy-algorithms/, https://www.geeksforgeeks.org/introduction-to-exchange-argument/]
tags: [greedy, algorithms]
---

# Greedy-Choice Property

**In one sentence, in my own words** — the licence a problem grants you to commit
to the locally best option and never look back: there is *some* optimal solution
that contains that local choice, so taking it cannot have cost you the optimum.

Introduced in [[../lessons/15-greedy|Lesson 15 — Greedy]]. It is a property of
the **problem**, not of the code. Your algorithm can be flawless and still wrong
because the problem never had it.

## When to reach for it

The trigger is not in the problem statement — it's in your own head, the moment
you catch yourself thinking *"I'll just always take the biggest one."* That
thought is a **claim**, and this is the page that says: state it, then attack it.

Softer statement-level tells that greedy is plausible:

- The answer is a single number (a max, a min, a count, a yes/no), not a
  reconstructed combination
- Every element gets passed exactly once and has one obvious best local action
- Sorting the input first makes a rule appear

Tells that it is **not** there:

- Choices interact — taking A changes what B is worth
- There's a budget or capacity being divided among items (knapsack shape)
- The brute force is a decision tree with overlapping subtrees → that's
  [[../lessons/13-1d-dynamic-programming|DP]]

## How it works

Two properties license a greedy algorithm:

1. **Greedy-choice property** — an optimal solution exists that contains the
   locally-best choice.
2. **Optimal substructure** — what remains after that choice is a smaller
   instance of the same problem.

DP needs (2) as well. **(1) is the part DP does not need, and the part that
fails.** That asymmetry is the entire difference between the two families.

**The exchange argument** is the standard proof shape: take an optimal solution
that does *not* start with your greedy choice, swap the greedy choice in, show
the result is no worse. If the swap always works, a greedy start was safe;
induct on what remains.

**The practical interview move**, since you will not write a proof in 45 minutes:
say the claim in one sentence, then spend thirty seconds genuinely trying to
build an input that breaks it. Narrate the attack. It catches most wrong
greedies and it is what earns the interviewer's trust.

## Minimal example

The canonical failure. Make 6 from `{1, 3, 4}`, fewest coins:

```
greedy (largest coin that fits):   4 + 1 + 1   →  3 coins
optimal:                           3 + 3       →  2 coins
```

Taking the locally best coin `4` destroyed the `3 + 3` structure.

The same greedy **is** optimal on US denominations `{1, 5, 10, 25}`. So "greedy
works for coin change" is a fact about a coin set, not about the problem — and
the algorithm stops being correct silently when the input distribution changes.
This is why Coin Change sits in
[[../lessons/13-1d-dynamic-programming|Topic 13]] rather than Topic 15.

## Complexity

Not an algorithm, so no cost of its own. But the payoff when the property holds
is consistent: a greedy scan is **O(n) time, O(1) space** where the DP fallback
is O(n) space or worse, because greedy stores a couple of running variables
instead of a table of subproblem answers. Sort-then-sweep greedies are
**O(n log n)** — the sort dominates.

## Gotchas

- **Passing the given examples proves nothing.** `{1,3,4}` greedy is correct for
  every amount 1–5 and wrong at 6.
- **"It's greedy" is a description, not a justification.** Name the claim.
- Reaching for greedy on anything knapsack-shaped.
- Assuming a greedy that works on one input distribution works on all of them.

## Related

- [[kadane]] — a greedy that is simultaneously a DP; the place the two families
  touch, rather than diverge
- [[reachability-frontier]] — a greedy whose safety comes from the frontier being
  monotone, so no later choice can ever want an earlier one back
- [[two-pointers]] — often the *implementation* of a sort-then-sweep greedy, but
  a scanning technique rather than a correctness argument

## Evidence

Problems where I used this unaided:
