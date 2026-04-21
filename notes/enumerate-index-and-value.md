# enumerate — index + value

**TL;DR:** `for i, val in enumerate(arr)` gives index and value in one iteration. Replaces `for i in range(len(arr)): val = arr[i]`.

**Patterns:** hash_map, index_tracking

## Where it matters — with usage

### LC 1 Two Sum — build `{val: i}` and check complement
```python
def twoSum(nums, target):
    seen = {}
    for i, num in enumerate(nums):
        if target - num in seen:
            return [seen[target - num], i]   # answer is INDICES, not values
        seen[num] = i                         # stash value → index for later lookup
```

### LC 121 Best Time to Buy/Sell Stock — track min price but return profit
```python
def maxProfit(prices):
    min_price = float('inf')
    best = 0
    for i, p in enumerate(prices):
        min_price = min(min_price, p)
        best = max(best, p - min_price)      # i not used here, but enumerate makes it trivial to add "day bought/sold"
    return best
```

### LC 448 Find All Numbers Disappeared — index-as-marker
```python
def findDisappeared(nums):
    for i, num in enumerate(nums):
        idx = abs(num) - 1                    # use value as 0-based index
        nums[idx] = -abs(nums[idx])           # mark "seen" by negating
    return [i + 1 for i, n in enumerate(nums) if n > 0]   # positives = unseen numbers
```

### LC 287 Find the Duplicate — index-value pairing for cycle detection
```python
# Floyd's cycle detection treats nums[i] as "next pointer" — enumerate not needed,
# but variants use enumerate to find the INDEX where the duplicate lands
```

### Dict comprehension pattern (LC 1 one-liner precompute)
```python
index_map = {num: i for i, num in enumerate(nums)}   # last-index-wins for duplicates
```

## Variants

```python
for i, val in enumerate(arr, start=1):       # i starts at 1 (LC 136 single number, 1-indexed output)
    ...

for i, val in enumerate(reversed(arr)):      # i counts forward over reversed iteration
    original_index = len(arr) - 1 - i
```

**Gotcha:** If you ignore `i` throughout the loop, drop it: `for val in arr`. Using `enumerate` without needing the index is a micro-signal of tool overuse.
