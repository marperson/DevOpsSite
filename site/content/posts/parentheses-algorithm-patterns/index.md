---
title: "Three Ways to Solve Parentheses Problems: Backtracking, Greedy, and the Fake Stack"
date: 2026-10-11
draft: false
description: "A walkthrough of three classic LeetCode parentheses problems, each solved with a different algorithmic paradigm: backtracking with pruning, greedy range-tracking, and a counter-based 'fake stack'."
tags: ["algorithms", "leetcode", "backtracking", "greedy", "stack", "data-structures", "interview-prep", "tutorial"]
featuredImage: "feature.svg"
---

# Three Ways to Solve Parentheses Problems: Backtracking, Greedy, and the Fake Stack

Parentheses problems are a deceptively good teaching tool. The input alphabet is tiny — just `(`, `)`, and sometimes `*` — yet the family of problems built on it touches three completely different algorithmic paradigms: **backtracking**, **greedy**, and **stack simulation**. Working through three LeetCode problems back to back makes the contrast between these approaches much clearer than studying them in isolation.

This post covers:

- [LeetCode 22 — Generate Parentheses](https://leetcode.com/problems/generate-parentheses/) → Backtracking
- [LeetCode 678 — Valid Parenthesis String](https://leetcode.com/problems/valid-parenthesis-string/) → Greedy
- [LeetCode 921 — Minimum Add to Make Parentheses Valid](https://leetcode.com/problems/minimum-add-to-make-parentheses-valid/) → Counter-based "fake stack"

---

## 1. Generate Parentheses — Backtracking

**Problem:** given `n` pairs of parentheses, generate all combinations of well-formed parentheses strings.

Backtracking is best understood as walking through a maze: at every junction you make a choice and drop a breadcrumb, you follow that path to its end, and if it's a dead end (or a valid answer) you **retreat to the last junction** and try the road not taken.

### The decision tree

At each position in the string, there are at most two choices:

- **Choice A** — add `(`, as long as you haven't used all `n` opens (`left < n`)
- **Choice B** — add `)`, as long as it won't outnumber the opens already placed (`right < left`)

```python
from typing import List

def generateParenthesis(n: int) -> List[str]:
    result = []

    def backtrack(current: str, left: int, right: int) -> None:
        if len(current) == 2 * n:
            result.append(current)
            return
        if left < n:
            backtrack(current + "(", left + 1, right)
        if right < left:
            backtrack(current + ")", left, right + 1)

    backtrack("", 0, 0)
    return result
```

### How the "retreat" actually happens

For `n = 2`, tracing the call stack shows the backtracking in action:

1. Start: `""` (left: 0, right: 0)
2. Choose `(`: `"("` (left: 1, right: 0)
3. Choose `(` again: `"(("` (left: 2, right: 0) — left is now exhausted, Choice A is no longer available
4. Forced to choose `)`: `"(()"` (left: 2, right: 1)
5. Forced to choose `)`: `"(())"` (left: 2, right: 2) — **first valid answer found!**
6. The call that produced `"(())"` returns, and control **unwinds** to the `"(()"` frame.
7. `"(()"` has no other option either, so it unwinds again, all the way back to `"("` (left: 1, right: 0).
8. At that junction, the "all `(`" branch has been fully explored — the algorithm now tries the other branch: choose `)`.
9. That leads to `"()"` (left: 1, right: 1), and the search continues from there.

The recursive call stack *is* the breadcrumb trail. No extra bookkeeping is required — returning from a call is the backtrack step.

### Pruning

A brute-force approach would generate every string of length `2n` over `{(, )}` and filter the valid ones afterward. Backtracking is smarter: the conditions `left < n` and `right < left` **prune** branches that can never lead to a valid answer before they're even explored. For example, the search can never start with `)`, because at the root `right < left` is `0 < 0`, which is false — that entire branch is cut off at depth zero.

---

## 2. Valid Parenthesis String — Greedy

**Problem:** given a string containing `(`, `)`, and `*` (where `*` can stand in for `(`, `)`, or an empty string), determine whether it's possible to interpret the wildcards so the result is a valid parentheses string.

Greedy algorithms follow one rule: **at every step, make the choice that looks best right now**, without reconsidering it later. Unlike backtracking, a greedy algorithm doesn't backtrack — once a decision is made, it's final.

### When does greedy actually reach the global optimum?

Greedy only guarantees a correct answer when the problem has two properties:

1. **Greedy-choice property** — a globally optimal solution can be reached by a sequence of locally optimal choices.
2. **Optimal substructure** — an optimal solution to the problem contains optimal solutions to its subproblems.

| Works with greedy | Doesn't work with greedy |
|---|---|
| **Dijkstra's shortest path** — always expand the unvisited node closest to the source | **0-1 Knapsack** — greedily taking the highest value-per-weight item can leave the knapsack under-filled, giving a worse total than dynamic programming |
| **Kruskal / Prim MST** — always add the cheapest safe edge | |
| **Huffman coding** — always merge the two lowest-frequency nodes | |

Greedy's appeal is simplicity and speed — usually linear or near-linear time. Its risk is that if the greedy-choice property doesn't hold, you silently get a suboptimal (or outright wrong) answer with no indication that anything went wrong.

### Applying it to LeetCode 678

Instead of trying every assignment of `*`, track the **range of possible "open paren count"** values as you scan left to right: `low` (the minimum possible open count, treating every `*` as `)` or empty) and `high` (the maximum, treating every `*` as `(`).

```python
def checkValidString(s: str) -> bool:
    low = high = 0
    for c in s:
        if c == '(':
            low += 1
            high += 1
        elif c == ')':
            low -= 1
            high -= 1
        else:  # '*' — wildcard
            low -= 1
            high += 1

        if high < 0:
            # even the most generous interpretation has too many ')'
            return False
        low = max(low, 0)  # can't have a negative open count

    return low == 0
```

The greedy insight: you never need to know *which specific* interpretation of each `*` worked — only whether **some** value in `[low, high]` can reach exactly `0` opens with no step ever going negative. Collapsing all the branching possibilities into a single range turns an exponential search into one linear pass.

---

## 3. Minimum Add to Make Parentheses Valid — The "Fake Stack"

**Problem:** given a string of `(` and `)`, find the minimum number of parentheses you'd need to insert anywhere to make it valid.

The textbook way to validate parentheses is to push every `(` onto a stack and pop when you see a matching `)`. But for this problem you only ever care about **how many** unmatched opens are currently pending — not their position or identity. So instead of a real stack, a single integer counter plays the same role: push becomes `+1`, pop becomes `-1`. That's the "fake stack" — same semantics, no actual stack data structure.

```python
def minAddToMakeValid(s: str) -> int:
    open_needed = 0   # depth of the "stack" — unmatched '(' count
    additions = 0      # ')' inserts needed so far

    for c in s:
        if c == '(':
            open_needed += 1
        else:  # c == ')'
            if open_needed > 0:
                open_needed -= 1   # matches a pending '('
            else:
                additions += 1     # no '(' to match — needs one inserted

    # any '(' still unmatched at the end needs a closing ')'
    return additions + open_needed
```

Walking through `"(()))("`:

| char | `open_needed` | `additions` | note |
|---|---|---|---|
| `(` | 1 | 0 | push |
| `(` | 2 | 0 | push |
| `)` | 1 | 0 | pop |
| `)` | 0 | 0 | pop |
| `)` | 0 | 1 | nothing to pop → needs a `(` inserted |
| `(` | 1 | 1 | push |

Final answer: `additions + open_needed = 1 + 1 = 2`.

---

## Comparing the three approaches

| | Backtracking (LC 22) | Greedy (LC 678) | Fake Stack (LC 921) |
|---|---|---|---|
| **Goal** | Enumerate *all* valid combinations | Decide if *any* valid interpretation exists | Count *minimum edits* to make valid |
| **Core idea** | DFS + undo (via the call stack) | Track a range of possibilities, collapse to one pass | Replace a stack with a counter when only depth matters |
| **Revisits past choices?** | Yes — that's the whole point | No — never reconsiders | No — single forward pass |
| **Time complexity** | Exponential (bounded by Catalan number) | O(n) | O(n) |
| **When to reach for it** | Need every valid output, not just a yes/no | Need a feasibility check, and local choices compose into a global answer | Need a count/transform, and only the aggregate "balance" matters, not full history |

The common thread: all three problems are about **balance between opens and closes**, but the question being asked — *generate all*, *is it possible*, or *what's the minimum fix* — determines which paradigm fits. Recognizing that shape before reaching for a stack (or a backtracking template) by default is the actual skill being practiced here.

---

## Key Takeaways

- ✅ Backtracking = incremental choice + recursion + pruning; the "undo" is free because it's just a function return
- ✅ Greedy only reaches the global optimum when the greedy-choice property and optimal substructure both hold — otherwise it silently gives a suboptimal answer
- ✅ When a stack-based simulation only needs to know *depth*, not *contents*, a plain counter is simpler and just as correct — a "fake stack"
- ✅ The same surface-level problem (parentheses) can map to three unrelated paradigms depending on exactly what's being asked
