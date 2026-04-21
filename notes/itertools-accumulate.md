# itertools.accumulate — prefix sums & running ops

**TL;DR:** `accumulate(arr)` returns running sums. Pass a function as second arg for running max / min / product / custom reduction.

**Patterns:** prefix_sum, running_reduction

```python
from itertools import accumulate
import operator

list(accumulate([1,2,3,4]))                    # [1, 3, 6, 10]
list(accumulate([1,2,3,4], initial=0))         # [0, 1, 3, 6, 10]  (Py 3.8+)
list(accumulate([1,2,3,4], operator.mul))      # [1, 2, 6, 24]
list(accumulate([3,1,4,1,5,9], max))           # [3, 3, 4, 4, 5, 9]
```

## Where it matters — with usage

### LC 1480 Running Sum — literally one line
```python
def runningSum(nums):
    return list(accumulate(nums))
```

### LC 303 Range Sum Query Immutable — precompute, then O(1) queries
```python
class NumArray:
    def __init__(self, nums):
        self.prefix = list(accumulate(nums, initial=0))    # len = n+1

    def sumRange(self, l, r):
        return self.prefix[r+1] - self.prefix[l]           # canonical range-query
```

### LC 560 Subarray Sum Equals K — prefix + hashmap
```python
def subarraySum(nums, k):
    prefix = [0]
    for x in accumulate(nums):
        prefix.append(x)
    # count pairs (i, j) where prefix[j] - prefix[i] == k
    seen = {0: 1}
    total = count = 0
    for x in nums:
        total += x
        count += seen.get(total - k, 0)
        seen[total] = seen.get(total, 0) + 1
    return count
```

### LC 53 Maximum Subarray — Kadane as a one-liner
```python
def maxSubArray(nums):
    return max(accumulate(nums, lambda acc, x: max(x, acc + x)))
    # if acc+x dips below x, reset to x — classic Kadane
```

### LC 724 Find Pivot Index — prefix vs total-prefix-self
```python
def pivotIndex(nums):
    total = sum(nums)
    prefix = 0
    for i, n in enumerate(nums):
        if prefix == total - prefix - n:
            return i
        prefix += n
    return -1
# alternative using accumulate:
# left = list(accumulate(nums, initial=0))[:-1]   # left sum before index i
```

### Running max / min use cases
```python
# stock-like "best so far"
best_so_far = list(accumulate(prices, max))       # running max
worst_so_far = list(accumulate(prices, min))      # running min
```

**Complexity:** O(n) time, O(n) space if materialized; O(1) as generator.

**Gotcha 1 — `initial=` only 3.8+.** LeetCode supports it. Workaround: `[0] + list(accumulate(nums))`.

**Gotcha 2 — result length.** Without initial: n. With initial: n+1. Off-by-one loves this.

**Gotcha 3 — func signature is `(acc, x)`.** Not `(x, acc)`. Matters for non-commutative ops like subtraction.
