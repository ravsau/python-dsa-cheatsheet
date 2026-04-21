# @cache for memoization

**TL;DR:** `functools.cache` (Python 3.9+) turns any recursive function into a memoized top-down DP with one decorator. Zero boilerplate, interview-legit.

**Where it matters:**
- LC 70 Climbing Stairs — O(2^n) → O(n)
- LC 198 House Robber — same
- LC 322 Coin Change — O(amount^n) → O(amount·n)
- LC 139 Word Break — O(2^n) → O(n²)
- LC 198/213/337 House Robber family
- LC 746 Min Cost Climbing Stairs
- LC 509 Fibonacci
- any recursive tree/grid DP with overlapping subproblems

**Patterns:** dp_1d, dp_2d, memoization, top_down_dp

**Code:**
```python
from functools import cache

class Solution:
    def climbStairs(self, n: int) -> int:
        @cache
        def dp(i):
            if i <= 1: return 1
            return dp(i-1) + dp(i-2)
        return dp(n)
```

**Before 3.9 (or if you want max-size):**
```python
from functools import lru_cache
@lru_cache(maxsize=None)
def dp(i): ...
```

**Rules:**
- Arguments must be **hashable** (no lists, no dicts — use tuples, frozensets)
- Define `dp` **inside** the outer method so cache doesn't leak across test cases
- State is whatever changes between calls (index, remaining capacity, flags)

**Converting recursion → DP:**
1. Write the naive recursion.
2. Identify state: "what tuple of args uniquely determines the answer?"
3. Add `@cache`. Done. O(states × work-per-state).

**Complexity analysis:**
- Time: `#unique states × work per state`
- Space: `#unique states` (cache) + recursion depth (stack)

**Gotcha 1 — mutable state in args.** Pass a tuple, not a list: `dp(i, tuple(mask))` not `dp(i, list)`.

**Gotcha 2 — cache persists across Solution instances if defined at module level.** LeetCode reuses the class between test cases — your cache from test 1 may leak into test 2. Always nest `@cache def dp()` inside the method.

**Gotcha 3 — recursion depth.** Python default limit is 1000. For n > 1000 (string DP, grid DP), either bump `sys.setrecursionlimit(10_000)` or convert to bottom-up iterative DP.
