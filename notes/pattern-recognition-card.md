# Pattern recognition card — 15 must-know for Medium-cap interviews

**TL;DR:** ~15 patterns cover ~90% of Google Medium-cap problems. Memorize SIGNAL → PATTERN. Use during verbal narration: *"This is X because Y."*

**Patterns:** pattern_recognition, interview_skill, verbal_narration

## The 15 patterns to recognize cold

### Array / String

| # | Pattern | Signal | Sample LC |
|---|---|---|---|
| 1 | Hash map lookup | "find pairs summing to X," "first duplicate," "anagram" | 1, 217, 49 |
| 2 | Two-pointer converging | sorted array + "pair finding" | 11, 15, 125 |
| 3 | Sliding window | "longest/shortest [substring/subarray] with constraint" | 3, 76, 209, 424 |
| 4 | Binary search | sorted + "find target" or "min value such that" | 704, 33, 875 |
| 5 | Sort + sweep | "intervals," "merge," "meeting rooms" | 56, 252, 253 |

### Linked list

| # | Pattern | Signal | Sample LC |
|---|---|---|---|
| 6 | Dummy head + tail walker | "merge sorted lists," "remove nth from end" | 21, 19, 86 |
| 7 | Fast/slow pointers | "cycle detection," "find middle" | 141, 142, 876 |
| 8 | LL reversal | "reverse list," "reverse k-group" | 206, 92, 25 |

### Trees

| # | Pattern | Signal | Sample LC |
|---|---|---|---|
| 9 | Tree DFS recursive | any tree without "level" | 104, 226, 543 |
| 10 | Tree BFS level order | "level order," "right side view" | 102, 199 |

### Graphs

| # | Pattern | Signal | Sample LC |
|---|---|---|---|
| 11 | Graph DFS/BFS on grid | "number of islands," "connected components" | 200, 695, 130 |
| 12 | BFS for shortest path | "minimum steps," "spreading," "minimum minutes" | 994, 1091, 542 |

### Heap, DP, Backtracking

| # | Pattern | Signal | Sample LC |
|---|---|---|---|
| 13 | Top-K with min-heap | "kth largest/smallest," "top k frequent" | 215, 347, 703 |
| 14 | DP 1D | "number of ways," "max/min value" + overlapping subproblems | 70, 198, 53, 322 |
| 15 | Backtracking | "all subsets/permutations/combinations" | 78, 46, 17, 39 |

## Tier 2 — optional

DP 2D (LCS, edit distance), monotonic stack (next greater element), topological sort (course schedule), union-find, trie. Recognize but don't sweat if untested.

## Tier 3 — skip for Medium-cap

Greedy, bit manipulation, math/geometry, Dijkstra. Almost never tested.

## Verbal phrase for each

### Array
> *"Hash map for O(1) lookup."*
> *"Two-pointer converging from both ends."*
> *"Sliding window — expand right, shrink left when [condition]."*
> *"Binary search, O(log N)."*
> *"Sort by start, linear sweep merging overlaps."*

### LL
> *"Dummy head as anchor, tail as cursor."*
> *"Fast/slow Floyd's pointers."*
> *"Three-way pointer swap for reversal."*

### Trees
> *"Tree DFS, three-piece scaffold."*
> *"BFS with deque, size snapshot per level."*

### Graphs
> *"DFS/BFS on grid, four-direction neighbors."*
> *"BFS — each level = one step. Multi-source if multiple starts."*

### Heap / DP / Backtracking
> *"Min-heap of size k. heap[0] = kth largest. O(N log K)."*
> *"DP. dp[i] means [X]. Recurrence based on last decision."*
> *"Backtracking. Choose, recurse, un-choose."*

## Decision flow during interview

```
Hear problem (30 seconds)
    ↓
Run signal check:
  - Sorted + pairs/palindrome? → two-pointer
  - Substring/subarray with constraint? → sliding window
  - Tree with "level"? → BFS
  - Tree without "level"? → DFS
  - Grid + reachability? → DFS
  - Grid + shortest path? → BFS
  - "All subsets/perms"? → backtracking
  - "Top K"? → heap
  - "Number of ways" / "max with overlap"? → DP
  - Intervals? → sort + sweep
  - Sorted + find? → binary search
  ↓
Say pattern aloud: "This is X because Y."
    ↓
Plan + code
```

## Practice routine

For 1 week before interview:
1. Read 5-10 problems daily (don't solve — just read)
2. For each, say the pattern aloud BEFORE looking at solution
3. Check yourself

After 30+ classifications, recognition becomes reflexive in <10 seconds.

## When you can't identify

Fallbacks:
- *"Let me start with brute force — O(N²) by [X]. Now let's see if we can optimize."*
- *"I'm considering [approach A] vs [approach B]. The tradeoff is..."*
- *"I'm not sure of the optimal pattern. Let me try [tool] and see if it fits."*

Asking for a hint after 5+ minutes is fine: *"I'm leaning toward [X]. Does that direction make sense?"*

## TL;DR

> **15 patterns × 1 signal each. Memorize the mapping. Verbalize "This is X because Y" within 30 seconds of hearing the problem. Reflexive recognition is what separates a Hire from a Strong Hire signal.**
