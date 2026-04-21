# itertools.pairwise — sliding window of 2

**TL;DR:** `pairwise(iterable)` yields consecutive pairs `(a, b)`. Replaces `for i in range(len(arr)-1): a, b = arr[i], arr[i+1]`.

**Patterns:** adjacent_check, sliding_window_size_2

```python
from itertools import pairwise
list(pairwise([1,2,3,4]))      # [(1,2), (2,3), (3,4)]
```

## Where it matters — with usage

### LC 896 Monotonic Array — clean adjacent check
```python
def isMonotonic(nums):
    return (all(a <= b for a, b in pairwise(nums))
         or all(a >= b for a, b in pairwise(nums)))
```

### LC 217 Contains Duplicate (sorted variant) — adjacent equality
```python
def containsDuplicate(nums):
    return any(a == b for a, b in pairwise(sorted(nums)))
    # O(n log n). For O(n), use: len(nums) != len(set(nums))
```

### LC 1. Longest Continuously Increasing Subarray (LC 674) — run length
```python
def findLengthOfLCIS(nums):
    if not nums: return 0
    best = cur = 1
    for a, b in pairwise(nums):
        cur = cur + 1 if b > a else 1             # reset or extend
        best = max(best, cur)
    return best
```

### LC 1920 Build Array — adjacent index access without `arr[i+1]` arithmetic
```python
# (Not the actual LC 1920 but demonstrates the pattern)
diffs = [b - a for a, b in pairwise(arr)]         # consecutive diffs
```

### LC 164 Maximum Gap (naive) — diffs after sort
```python
def maximumGap(nums):
    if len(nums) < 2: return 0
    return max(b - a for a, b in pairwise(sorted(nums)))
    # optimal uses bucket sort; this is O(n log n) but interview-acceptable
```

### LC 2696 Minimum String Length — adjacent removal via stack (pairwise on stack)
```python
# while stack reducible on pairs — pairwise helps articulate the check
```

## Fallback for Python < 3.10

```python
# same behavior, any Python version
pairs = zip(arr, arr[1:])
for a, b in pairs: ...

# or with islice
from itertools import islice
pairs = zip(arr, islice(arr, 1, None))
```

**Complexity:** O(n) time, O(1) space (generator).

**Gotcha 1 — Python 3.10+ only.** LeetCode runs 3.10+. On older interpreters fall back to `zip(arr, arr[1:])`.

**Gotcha 2 — strings yield char pairs.** `pairwise("abcd")` → `('a','b'),('b','c'),('c','d')`. Useful for LC 1957 / character-adjacency problems.

**Gotcha 3 — empty/single-element inputs.** `pairwise([])` and `pairwise([5])` yield nothing — no pair possible. Your `all(...)` becomes vacuously True, `any(...)` becomes False. Guard if the problem needs a special answer for these cases.
