# bisect for sorted-array insertion

**TL;DR:** `bisect` is binary search you don't have to write. `bisect_left` finds the leftmost insertion index; `insort` inserts while keeping the array sorted.

**Where it matters:**
- LC 35 Search Insert Position — literally `bisect_left(nums, target)`
- LC 704 Binary Search — `idx = bisect_left(nums, target); return idx if idx < n and nums[idx] == target else -1`
- LC 300 Longest Increasing Subsequence — patience sorting, `bisect_left` each element
- LC 354 Russian Doll Envelopes — sort + LIS via bisect
- LC 4 Median of Two Sorted Arrays — bisect-on-answer variant
- LC 981 Time Based Key-Value Store — bisect on timestamp list

**Patterns:** binary_search, LIS_patience, sorted_maintenance

**API:**
```python
import bisect
bisect.bisect_left(arr, x)     # leftmost index where x could be inserted
bisect.bisect_right(arr, x)    # rightmost index (= bisect)
bisect.insort_left(arr, x)     # insert, keeping sorted; left bias on ties
bisect.insort_right(arr, x)    # = insort
```

**Behavior on duplicates (arr = [1,2,2,2,3]):**
- `bisect_left(arr, 2)` → 1 (first 2)
- `bisect_right(arr, 2)` → 4 (after last 2)
- Count of 2s = `bisect_right - bisect_left` = 3

**LIS via patience sorting (LC 300) — O(n log n):**
```python
tails = []
for x in nums:
    i = bisect.bisect_left(tails, x)
    if i == len(tails):
        tails.append(x)           # extends LIS
    else:
        tails[i] = x              # replaces — keeps tails minimal
return len(tails)
```
**Key insight:** `tails` is NOT the actual LIS. It's the smallest tail value of every increasing subseq of length `i+1`. Its *length* equals the LIS length.

**Complexity:**
- `bisect_left/right` — O(log n)
- `insort_left/right` — O(n) (list shift dominates)

**Gotcha 1 — `insort` is O(n), not O(log n).** The search is O(log n) but the actual insertion shifts elements. If you need O(log n) insert, use a balanced BST / SortedList (`sortedcontainers.SortedList`).

**Gotcha 2 — `key=` arg only in Python 3.10+.** Before that, bisect on a parallel keys array or use SortedList.

**Gotcha 3 — `bisect_left` vs `bisect_right` picking.** Rule: `bisect_left` for "first ≥ x" questions, `bisect_right` for "first > x". LIS uses `bisect_left` (strictly increasing); for non-strict ≥, use `bisect_right`.
