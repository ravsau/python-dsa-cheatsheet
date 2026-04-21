# float('inf') / float('-inf') — sentinels for min/max

**TL;DR:** `float('inf')` is infinity — larger than any finite number. Use as initial value for "find min" problems. `float('-inf')` for max.

**Patterns:** dp_minimization, graph_distance, sentinel

```python
float('inf')                  # +infinity
float('-inf')                 # -infinity
import math
math.inf; -math.inf           # same thing, cleaner import
```

## Where it matters — with usage

### LC 64 Minimum Path Sum — DP grid init to inf
```python
def minPathSum(grid):
    m, n = len(grid), len(grid[0])
    dp = [[float('inf')] * n for _ in range(m)]
    dp[0][0] = grid[0][0]
    for r in range(m):
        for c in range(n):
            if r == 0 and c == 0: continue
            top  = dp[r-1][c] if r > 0 else float('inf')
            left = dp[r][c-1] if c > 0 else float('inf')
            dp[r][c] = grid[r][c] + min(top, left)      # inf "excluded" naturally
    return dp[-1][-1]
```

### LC 322 Coin Change — unreachable = inf
```python
def coinChange(coins, amount):
    dp = [float('inf')] * (amount + 1)
    dp[0] = 0
    for a in range(1, amount + 1):
        for c in coins:
            if c <= a:
                dp[a] = min(dp[a], dp[a-c] + 1)
    return dp[amount] if dp[amount] != float('inf') else -1
    # inf signals "impossible" → return -1
```

### LC 743 Network Delay Time — Dijkstra with inf distances
```python
import heapq
def networkDelayTime(times, n, k):
    graph = defaultdict(list)
    for u, v, w in times: graph[u].append((v, w))

    dist = [float('inf')] * (n + 1)
    dist[k] = 0
    heap = [(0, k)]
    while heap:
        d, u = heapq.heappop(heap)
        if d > dist[u]: continue
        for v, w in graph[u]:
            if d + w < dist[v]:
                dist[v] = d + w
                heapq.heappush(heap, (dist[v], v))

    ans = max(dist[1:])                          # skip dist[0]
    return ans if ans != float('inf') else -1    # inf → unreachable
```

### LC 787 Cheapest Flights — Bellman-Ford with inf
```python
def findCheapestPrice(n, flights, src, dst, k):
    prices = [float('inf')] * n
    prices[src] = 0
    for _ in range(k + 1):
        tmp = prices[:]
        for u, v, w in flights:
            if prices[u] != float('inf') and prices[u] + w < tmp[v]:
                tmp[v] = prices[u] + w
        prices = tmp
    return prices[dst] if prices[dst] != float('inf') else -1
```

### LC 121 Best Time to Buy/Sell Stock — min tracking
```python
def maxProfit(prices):
    min_p = float('inf')
    best = 0
    for p in prices:
        min_p = min(min_p, p)
        best = max(best, p - min_p)
    return best
```

### LC 84 Largest Rectangle in Histogram — stack with sentinel
```python
def largestRectangleArea(heights):
    heights = [0] + heights + [0]           # sentinels simplify boundary cases
    # (float('-inf') not needed here; 0 works as "stops popping" sentinel)
```

## Comparison semantics

```python
float('inf') > 10**100        # True — beats any finite number
float('inf') == float('inf')  # True
float('inf') - float('inf')   # nan — CAREFUL
float('inf') + 1              # inf
float('-inf') < -10**100      # True
```

## Alternatives

```python
INT_MAX = 2**31 - 1           # when problem says "32-bit signed"
import sys
sys.maxsize                   # platform max int (usually 2^63 - 1)
```

**When to use which:**
- DP init / "unreached" distance → `float('inf')`
- Problem explicitly says "32-bit" → `INT_MAX = 2**31 - 1`
- Need exact int arithmetic only → `sys.maxsize`

**Gotcha 1 — `inf` is a float, not int.** Putting it in an int-typed `dp` array forces float conversion throughout. Usually harmless; matters in tight numerical loops.

**Gotcha 2 — `inf - inf == nan`.** `nan` propagates silently; `nan == anything` is False. If your DP ever subtracts inf from inf, you get silently wrong results.

**Gotcha 3 — `float('inf')` isn't valid JSON.** If output might be serialized, convert to `None` or `"Infinity"` at the boundary.
