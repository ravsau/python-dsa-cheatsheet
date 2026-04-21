# heapq has no max-heap — negate values

**TL;DR:** Python's `heapq` is a **min-heap only**. For max-heap behavior, push `-x`, pop and negate back.

**Where it matters:**
- LC 23 Merge k Sorted Lists — min-heap of heads
- LC 215 Kth Largest Element in an Array — min-heap of size k, or max-heap of n
- LC 295 Find Median from Data Stream — max-heap (low half) + min-heap (high half)
- LC 347 Top K Frequent Elements — min-heap of size k
- LC 703 Kth Largest in a Stream — min-heap of size k
- LC 973 K Closest Points to Origin — max-heap of size k (by distance)

**Patterns:** top_k, merge_k, median_stream, scheduling

**Operations:**
```python
import heapq
h = []
heapq.heappush(h, x)        # O(log n)
heapq.heappop(h)            # O(log n) → smallest
h[0]                        # O(1) → peek smallest
heapq.heapify(arr)          # O(n) → in-place
heapq.nlargest(k, arr)      # O(n log k)
heapq.nsmallest(k, arr)     # O(n log k)
```

**Max-heap via negation:**
```python
heapq.heappush(h, -x)
largest = -heapq.heappop(h)
```

**Tuple heaps (custom key):** heap compares tuples lexicographically.
```python
heapq.heappush(h, (freq, val))          # min-freq first
heapq.heappush(h, (-freq, val))         # max-freq first (negate)
heapq.heappush(h, (dist, point_id, pt)) # tie-break with unique id to avoid comparing unhashables
```

**Top-K pattern (min-heap of size k):**
```python
# O(n log k) — strictly better than O(n log n) sort when k << n
heap = []
for x in arr:
    heapq.heappush(heap, x)
    if len(heap) > k:
        heapq.heappop(heap)
return heap[0]   # kth largest
```

**Gotcha:** When heap elements are `(priority, object)` and two priorities tie, Python tries to compare the objects — crashes if they're not comparable (e.g. ListNode). Fix: insert a unique sequence counter as tiebreak: `(priority, counter, object)`.
