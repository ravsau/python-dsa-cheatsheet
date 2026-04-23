# Two-pass from both sides — compute left, compute right, combine

**TL;DR:** Many array problems ask "for each index, what's the answer considering everything on its left AND everything on its right?" Solution: one left-to-right pass builds `left[i]` = aggregate of left side; one right-to-left pass builds `right[i]` = aggregate of right side; combine them per index.

**Patterns:** prefix_suffix, two_pass, aggregate_both_sides

## The 3-step recipe

1. **Left pass** — `left[i] = some aggregate of nums[0..i-1]` (everything before i)
2. **Right pass** — `right[i] = some aggregate of nums[i+1..n-1]` (everything after i)
3. **Combine** — per-index function of `left[i]` and `right[i]`

The "aggregate" is usually product, sum, max, or min.

## Where it matters — with usage

### LC 238 Product of Array Except Self — prefix × suffix product
```python
def productExceptSelf(nums):
    n = len(nums)
    left = [1] * n
    right = [1] * n

    for i in range(1, n):
        left[i] = left[i-1] * nums[i-1]        # product of everything before i

    for i in range(n-2, -1, -1):
        right[i] = right[i+1] * nums[i+1]      # product of everything after i

    return [left[i] * right[i] for i in range(n)]
```

**Optimized (O(1) extra space — reuse output, roll right):**
```python
def productExceptSelf(nums):
    n = len(nums)
    answer = [1] * n
    for i in range(1, n):
        answer[i] = answer[i-1] * nums[i-1]    # answer holds LEFT products
    right = 1
    for i in range(n-1, -1, -1):
        answer[i] *= right                      # fold in rolling RIGHT product
        right *= nums[i]
    return answer
```

### LC 42 Trapping Rain Water — left_max × right_max (min determines water height)
```python
def trap(height):
    n = len(height)
    left_max = [0] * n
    right_max = [0] * n

    left_max[0] = height[0]
    for i in range(1, n):
        left_max[i] = max(left_max[i-1], height[i])     # tallest to the left (inclusive)

    right_max[-1] = height[-1]
    for i in range(n-2, -1, -1):
        right_max[i] = max(right_max[i+1], height[i])   # tallest to the right (inclusive)

    return sum(min(left_max[i], right_max[i]) - height[i] for i in range(n))
```

**Optimized to two pointers converging** — skipping the arrays, but same mental model.

### LC 123 Best Time to Buy/Sell Stock III — best profit left, best profit right
```python
def maxProfit(prices):
    n = len(prices)
    left_profit = [0] * n                        # max profit from one tx in prices[0..i]
    right_profit = [0] * n                       # max profit from one tx in prices[i..n-1]

    min_price = prices[0]
    for i in range(1, n):
        min_price = min(min_price, prices[i])
        left_profit[i] = max(left_profit[i-1], prices[i] - min_price)

    max_price = prices[-1]
    for i in range(n-2, -1, -1):
        max_price = max(max_price, prices[i])
        right_profit[i] = max(right_profit[i+1], max_price - prices[i])

    return max(left_profit[i] + right_profit[i] for i in range(n))
```

### LC 135 Candy — scan left-to-right for left neighbor, then right-to-left
```python
def candy(ratings):
    n = len(ratings)
    candies = [1] * n
    for i in range(1, n):
        if ratings[i] > ratings[i-1]:
            candies[i] = candies[i-1] + 1       # reward rising trend from left
    for i in range(n-2, -1, -1):
        if ratings[i] > ratings[i+1]:
            candies[i] = max(candies[i], candies[i+1] + 1)    # also from right
    return sum(candies)
```

### LC 724 Find Pivot Index — total-left == total-right
```python
def pivotIndex(nums):
    total = sum(nums)
    left = 0
    for i, n in enumerate(nums):
        right = total - left - n
        if left == right:
            return i
        left += n
    return -1
# uses a rolling left + derived right, no array needed
```

## The space-optimization ladder

Every two-pass problem admits space reductions if you look:

| Level | Storage | Feel |
|---|---|---|
| **L1** | 2 arrays `left[], right[]` | obvious, readable |
| **L2** | 1 array + 1 rolling variable | common optimization |
| **L3** | 0 extra arrays — two pointers + two rolling variables | cleverest (LC 42 optimized) |

**Always start with L1 in an interview.** Then say: "I can optimize to L2/L3 — want me to?" That shows you see the space cost AND know the fix, while not over-complicating the first pass.

## Why the pattern exists

A lot of problems have the shape: "answer at i depends on everything NOT at i." Computing that naively is O(n²). The two-pass trick splits "not i" into "left of i" and "right of i," each of which can be built incrementally — turning O(n²) into O(n).

## Gotchas

**Gotcha 1 — right pass direction.** `range(n-2, -1, -1)` is the shape for "from n-2 down through 0." Off-by-one here is the #1 bug. Trace a small example.

**Gotcha 2 — boundary values.** `left[0]` and `right[n-1]` represent "nothing to the left/right" — initialize to the identity of the aggregate (0 for sum, 1 for product, -inf for max, +inf for min).

**Gotcha 3 — confusing "inclusive" vs "exclusive" ends.** `left[i]` can mean "everything through i" or "everything before i" — pick one convention and commit. The LC 42 version above is inclusive; the LC 238 version is exclusive. Both are valid, but you must not mix.

**Gotcha 4 — optimizing prematurely.** Starting with the one-array form confuses you mid-implementation. Write the two-array version, get it passing, THEN fold.
