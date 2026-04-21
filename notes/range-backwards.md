# range() backwards

**TL;DR:** `range(n-1, -1, -1)` iterates `n-1, n-2, ..., 0`. Memorize this shape — it's the go-to for index-based reverse iteration.

**Where it matters:**
- LC 42 Trapping Rain Water — second pass right-to-left for max_right
- LC 198 House Robber — fill DP table from the end
- LC 496 Next Greater Element — monotonic stack walking right-to-left
- LC 84 Largest Rectangle in Histogram — same, from both ends
- LC 121 Best Time to Buy Stock — track max from right
- LC 238 Product of Array Except Self — right product pass
- any DP / monotonic-stack problem filled in reverse

**Patterns:** reverse_iteration, monotonic_stack, dp_right_to_left

**The four useful shapes:**
```python
range(n)                  # 0, 1, ..., n-1        forward
range(n-1, -1, -1)        # n-1, n-2, ..., 0      reverse by index
range(start, stop, step)  # general form
range(0, n, 2)            # 0, 2, 4, ... even indices
range(1, n, 2)            # 1, 3, 5, ... odd indices
```

**Why the `-1` stop:** `range(a, b, step)` stops **before** `b`. To include `0`, stop must be `-1`.

**Common mistake:**
```python
# BUG — stops at 1, never processes index 0
for i in range(n-1, 0, -1):
    ...

# CORRECT — processes n-1 down to 0
for i in range(n-1, -1, -1):
    ...
```

**When you don't need the index, prefer `reversed()`:**
```python
for val in reversed(arr):         # cleaner, no bookkeeping
    ...

for i in range(len(arr)-1, -1, -1):  # only when i matters
    ...
```

**Right-pass for LC 238 style:**
```python
n = len(nums)
right = [1] * n
for i in range(n-2, -1, -1):       # n-2 down to 0
    right[i] = right[i+1] * nums[i+1]
```

**Gotcha 1 — `range(n, -1)` without step.** Default step is `+1`, so `range(n, -1)` is empty. You MUST pass `-1` as step.

**Gotcha 2 — step of 0 is ValueError.** Not zero-iteration, hard error. Guard before calling if step comes from user data.

**Gotcha 3 — range is lazy.** `range(10**18)` is instant and O(1) memory. Don't wrap in `list()` unless you need to, especially on large ranges.
