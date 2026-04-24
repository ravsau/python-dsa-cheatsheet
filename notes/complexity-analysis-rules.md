# Complexity analysis rules — the ones people miss

**TL;DR:** Two rules trip up 80% of candidates: recursion stack counts toward space, and heap ops scale with *heap size* (not input size).

**Patterns:** complexity_analysis

## Rule 1 — Recursion stack counts as space

Every recursive call pushes a frame onto the call stack. Space = max depth reached.

```python
def dfs(node):
    if not node: return 0
    return 1 + max(dfs(node.left), dfs(node.right))
```

| Input | Space |
|---|---|
| Balanced binary tree (n nodes) | **O(log n)** — height = log₂(n) |
| Skewed tree (linked-list shape) | **O(n)** — height = n |
| Purely iterative (no recursion) | exclude — no stack |

**Where it matters:**
- LC 104 Max Depth Binary Tree — O(log n) balanced, O(n) skewed
- LC 98 Validate BST — same
- LC 226 Invert Binary Tree — same
- LC 124 Binary Tree Max Path Sum — same
- LC 206 Reverse Linked List (recursive) — **O(n)** always (linked lists are "maximally skewed trees")

**Gotcha:** "I'm not creating any data structure" ≠ O(1) space. If there's recursion, include the stack.

## Rule 2 — Heap ops scale with HEAP size, not input size

```python
heapq.heappush(h, x)    # O(log |h|)  — log of current heap size
heapq.heappop(h)        # O(log |h|)
h[0]                    # O(1) — peek
heapq.heapify(arr)      # O(n) — surprisingly, not O(n log n)
```

**The Top-K pattern** — cap heap at k, process n items:

```python
heap = []
for x in arr:
    heapq.heappush(heap, x)
    if len(heap) > k:
        heapq.heappop(heap)
# heap holds the k largest/smallest (direction by variant)
```

- Time: **O(n log k)** — n pushes + n pops, each O(log k) since heap is bounded
- Space: **O(k)** — heap never exceeds k elements

**Compare approaches for "kth largest":**

| Approach | Time | Space |
|---|---|---|
| Full sort → `arr[-k]` | O(n log n) | O(n) |
| Heap of n → pop k times | O(n + k log n) | O(n) |
| **Heap of size k** | **O(n log k)** | **O(k)** |
| Quickselect (avg) | O(n) avg, O(n²) worst | O(1) |

Top-K heap beats full sort when k << n. That's the interview sweet spot.

**Where it matters:**
- LC 215 Kth Largest — top-K min-heap
- LC 347 Top K Frequent — top-K min-heap by frequency
- LC 703 Kth Largest in a Stream — maintain top-K heap across inserts
- LC 973 K Closest Points — top-K max-heap (or min-heap with distance key)
- LC 295 Find Median from Data Stream — two heaps, each bounded

## Rule 3 — Amortized vs worst-case

`list.append(x)` is **O(1) amortized**, not O(1) worst case. Occasionally the list triples its capacity and copies all elements, which is O(n). But averaged over n appends, that's O(1) each.

Same applies to:
- `dict[k] = v` — O(1) amortized (rehash is O(n) occasionally)
- `set.add(x)` — O(1) amortized
- `heapq.heappush` — O(log n) amortized in bad cases, but typically exactly log n

**When it matters:** in tight loops where worst-case matters (real-time systems, adversarial inputs). For interviews, amortized is usually fine — just say "O(1) amortized" to show you know the nuance.

## Rule 4 — Hidden costs in "clean" Python

Python's readable one-liners can hide real work:

```python
s[::-1]           # O(n) — creates a new string
arr.pop(0)        # O(n) — shifts all elements (use deque for O(1))
sorted(arr)       # O(n log n) — not free
set(arr)          # O(n) — iterates + hashes each element
"".join(parts)    # O(total length) — not O(len(parts))
min(arr, key=f)   # O(n) calls to f + O(n) compares
```

Interviewer will ask "what's the complexity of `sorted(arr)`?" — if you say O(n) you lose signal.

## Rule 5 — Nested loops aren't always O(n²) — check if pointers RESET

The most-missed rule. **Nested while doesn't multiply if the inner pointer never resets.**

```python
# O(n²) — inner ALWAYS starts at 0
for i in range(n):
    for j in range(n):              # fresh j every iteration
        ...

# O(n) — inner picks up where left off
l = 0
for r in range(n):
    while l < r and cond(l):        # l never resets to 0
        l += 1
```

In the second form, `l` only advances. Across all outer iterations, `l` moves at most `n` times TOTAL. So total inner work is `n`, not `n × n`.

**Where it matters:**
- LC 125 Valid Palindrome (two-pointer with skip-non-alnum) — O(n), not O(n²)
- LC 3 Longest Substring Without Repeating — sliding window, O(n)
- LC 76 Minimum Window Substring — sliding window, O(n)
- LC 209 Min Size Subarray Sum — sliding window, O(n)
- LC 26 Remove Duplicates — read/write pointers, O(n)

**The rule of thumb:** if both pointers move **monotonically** (only forward, no resets), total work is bounded by their max travel distance — usually O(n) total even with nested loops.

This is **amortized analysis** — average cost per outer iteration is constant, even if individual ones are expensive.

## Rule 6 — Subtract-a-constant is O(n); divide-by-a-constant is O(log n)

The shape of how your input shrinks each iteration determines the complexity class. This trips people up constantly.

```
"subtract 2 each step"     → n steps total → O(n)
"halve each step"          → log n steps   → O(log n)
```

| Shrink pattern | Example | Steps | Complexity |
|---|---|---|---|
| Subtract constant | `left += 1, right -= 1` (two-pointer swap) | n/2 | **O(n)** |
| Divide by 2 | binary search, halving each iter | log₂(n) | **O(log n)** |
| Subtract by √n | sieve of Eratosthenes edges | √n | O(√n) |
| Divide by k | log base k | log_k(n) | O(log n) (same class, constants drop) |

**The mental test:** *"Does each iteration remove a FIXED AMOUNT or a FIXED FRACTION?"*
- Fixed amount → linear (O(n))
- Fixed fraction → logarithmic (O(log n))

**Massive practical difference at n = 10⁶:**
| Complexity | Steps |
|---|---|
| O(n) | 1,000,000 |
| O(n/2) | 500,000 ← still O(n) |
| O(√n) | ~1,000 |
| O(log n) | ~20 |

Halving doesn't just make things faster — it changes the **category**.

**Where it matters:**
- LC 344 Reverse String — two-pointer swap, subtracts 2/iter → O(n)
- LC 125 Valid Palindrome — same shape → O(n)
- LC 704 Binary Search — halves per iter → O(log n)
- LC 33 Search in Rotated — halves per iter → O(log n)
- LC 74 Search 2D Matrix — binary search on flattened → O(log(mn))

**Gotcha:** "Reducing by a constant each iteration" feels like it should be fast — but it's still linear. Only DIVIDING (cutting by a factor) gets you log n.

## Rule 7 — Set/dict lookup is "O(1) expected," not "O(1) guaranteed"

Hash collisions make dict/set ops O(n) worst case (all keys land in same bucket). In practice this never happens with well-designed hash functions — but be ready to say "O(1) expected / O(n) worst" if asked for guarantees.

## The complexity self-check (run after every solve)

Ask yourself:
1. **Did I count recursion depth as space?**
2. **Are any data-structure ops O(log n) that I assumed O(1)?** (heap ops, tree ops, sorted containers)
3. **Am I sorting somewhere?** `sorted` / `.sort()` = O(n log n) hidden
4. **Am I slicing?** `arr[a:b]` = O(b-a) hidden copy
5. **Am I creating intermediate collections?** List comp / `list(gen)` / `set(iter)` = O(n) space each
6. **If I have nested loops, do the pointers RESET each iteration?** If they only advance monotonically, total is O(n), not O(n²).

If you answer "yes" to any, add (or subtract!) that cost from your analysis.

## The one-sentence interview line

> "Time O(?) because [what the loop does × how many elements]. Space O(?) because [data structures + recursion depth + intermediate collections]."

State it every time. Senior signal.
