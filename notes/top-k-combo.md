# "Counting + sorting + top K" — three ways

**TL;DR:** 30%+ of mediums ask some "top-K / bottom-K by frequency" variant. Three idiomatic solutions — pick based on k vs n and tiebreak needs.

**Where it matters:**
- LC 347 Top K Frequent Elements
- LC 451 Sort Characters By Frequency
- LC 692 Top K Frequent Words (needs lexical tiebreak)
- LC 973 K Closest Points to Origin
- LC 215 Kth Largest Element
- LC 1636 Sort Array by Increasing Frequency
- LC 703 Kth Largest Element in Stream

**Patterns:** top_k, frequency, heap

## The three solutions

### 1. `Counter.most_common(k)` — cleanest, no tiebreak control
```python
from collections import Counter
# LC 347 in one line
[v for v, _ in Counter(nums).most_common(k)]
```
- Complexity: **O(n log k)** — internal heap
- Use when: simple top-K, no custom tiebreak
- Problems: LC 347, 451

### 2. `sorted(... key=...)` — full control over tiebreak
```python
# LC 692 — freq desc, then word asc
c = Counter(words)
return sorted(c.keys(), key=lambda w: (-c[w], w))[:k]
```
- Complexity: **O(n log n)**
- Use when: you need lexical / multi-field tiebreak — `most_common` alone can't express this
- Problems: LC 692, 1636

### 3. `heapq.nlargest(k, ..., key=...)` — O(n log k) with custom key
```python
# LC 973 — k closest points by distance
import heapq
return heapq.nlargest(k, points, key=lambda p: -(p[0]**2 + p[1]**2))
# or more naturally:
return heapq.nsmallest(k, points, key=lambda p: p[0]**2 + p[1]**2)
```
- Complexity: **O(n log k)** — strictly better than sort when k << n
- Use when: k is much smaller than n, and you need a custom key
- Problems: LC 215, 973, 347 (optimized), streaming variants

## Decision table

| Situation | Use |
|---|---|
| Simple top-K by count, no tiebreak rules | `Counter.most_common(k)` |
| Need custom tiebreak (alphabetical, lex, multi-field) | `sorted(..., key=tuple_key)[:k]` |
| k << n and want best perf | `heapq.nlargest(k, ..., key=...)` |
| Streaming (elements arrive one by one) | Maintain min-heap of size k, push + pop-when-over-size |

## Stream variant — min-heap of size k (LC 703):
```python
import heapq
class KthLargest:
    def __init__(self, k, nums):
        self.k = k
        self.h = nums
        heapq.heapify(self.h)
        while len(self.h) > k:
            heapq.heappop(self.h)

    def add(self, val):
        heapq.heappush(self.h, val)
        if len(self.h) > self.k:
            heapq.heappop(self.h)
        return self.h[0]          # kth largest == min of top-k heap
```

## Interview tell

If asked to **optimize** a `sorted(...)[:k]` solution, immediately pivot to `heapq.nlargest`. That's the "I know the complexity classes" signal — O(n log n) → O(n log k).

If they push harder: mention **Quickselect** (`heapq.nsmallest` averages O(n) isn't true; true Quickselect is O(n) average but O(n²) worst). Know the tradeoff even if you don't implement it from scratch.

**Gotcha 1 — `most_common` tie order is insertion order, not lex.** LC 692 will fail on tied frequencies. Use `sorted` with explicit tuple key.

**Gotcha 2 — heap comparisons on tuples with non-comparable 2nd element.** If ties occur and the tied objects aren't comparable (e.g., ListNode), Python crashes. Add a unique sequence counter: `(-freq, counter, object)`.

**Gotcha 3 — `nlargest(k, ..., key=lambda)`.** The `key` arg is applied to each element — don't negate inside the key AND use `nlargest` (double reversal). Pick `nsmallest` if your key is naturally "smaller = better."
