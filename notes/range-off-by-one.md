# range() off-by-one — when to subtract 1

**TL;DR:** `range(n)` yields `0, 1, ..., n-1` (exactly n values). Subtract 1 from the bound **only** if your loop body reaches past the current index (e.g. accesses `arr[i+1]`). Otherwise `range(len(arr))` is correct.

**Patterns:** iteration_bounds, adjacent_access

## The rule

| Loop body accesses | Use |
|---|---|
| `arr[i]` only | `range(len(arr))` |
| `arr[i]` and `arr[i+1]` | `range(len(arr) - 1)` |
| `arr[i-1]` and `arr[i]` | `range(1, len(arr))` |
| `arr[i-1]`, `arr[i]`, `arr[i+1]` | `range(1, len(arr) - 1)` |

**Principle:** trim the bound on whichever side your loop body would go out of bounds.

## Where it matters — with usage

### LC 1 Two Sum — access only `arr[i]` → full range
```python
for i in range(len(nums)):                  # every index, no neighbor access
    if target - nums[i] in seen:
        return [seen[target - nums[i]], i]
    seen[nums[i]] = i
```

### LC 674 Longest Continuous Increasing — access `arr[i]` and `arr[i+1]` → subtract 1
```python
best = cur = 1
for i in range(len(nums) - 1):              # stops at len-2; safe to access nums[i+1]
    if nums[i] < nums[i+1]:
        cur += 1
        best = max(best, cur)
    else:
        cur = 1

# equivalent, no off-by-one worry:
# for a, b in pairwise(nums): ...
```

### LC 238 Product Except Self — left pass touches `arr[i-1]` → start at 1
```python
out = [1] * len(nums)
for i in range(1, len(nums)):               # start at 1; nums[i-1] is always valid
    out[i] = out[i-1] * nums[i-1]
```

### LC 42 Trapping Rain Water — inner cells only (skip first and last) → start at 1, trim end
```python
# you only care about cells with a left AND right neighbor
for i in range(1, len(height) - 1):         # skip index 0 and len-1
    ...
```

### LC 5 Longest Palindromic Substring — every index is a potential center → full range
```python
for i in range(len(s)):                     # every index, expand helper handles bounds
    expand(i, i)                             # odd-length center
    expand(i, i+1)                           # even-length center; i+1 bound checked inside expand
```

### LC 198 House Robber — access `dp[i-1]`, `dp[i-2]` → start at 2
```python
dp = [0] * len(nums)
dp[0] = nums[0]
dp[1] = max(nums[0], nums[1])
for i in range(2, len(nums)):               # start at 2; dp[i-1] and dp[i-2] both valid
    dp[i] = max(dp[i-1], dp[i-2] + nums[i])
```

### LC 496 Next Greater Element — reverse + access both ends
```python
for i in range(len(nums) - 1, -1, -1):      # reverse: len-1 down to 0
    # n-1 is valid (we're at it), arr[i+1] would be invalid only if we accessed it
    ...
```

## Decision recipe (for when you're mid-code and unsure)

1. Pick a concrete small example: `arr = [10, 20, 30]`, so `len(arr) = 3`.
2. Ask: what's the LAST valid value of `i` given my loop body?
   - If body does `arr[i]` only → last valid `i = 2` → need `range(3)` → `range(len(arr))`
   - If body does `arr[i+1]` → last valid `i = 1` (else `arr[3]` explodes) → need `range(2)` → `range(len(arr) - 1)`
3. Ask: what's the FIRST valid value of `i`?
   - If body does `arr[i-1]` → first valid `i = 1` → `range(1, ...)`

## Also: `range(a, b, step)` — endpoints are `[a, b)`

```python
range(0, 5)       # 0, 1, 2, 3, 4        — b is EXCLUSIVE
range(1, 5)       # 1, 2, 3, 4
range(0, 5, 2)    # 0, 2, 4
range(5, 0, -1)   # 5, 4, 3, 2, 1        — stops BEFORE 0
range(5, -1, -1)  # 5, 4, 3, 2, 1, 0     — goes through 0
```

**The `-1` on the stop in reverse** is because stop is exclusive. To *include* 0, stop must be `-1`. See [`range-backwards.md`](range-backwards.md).

## Fence-post mental model

Imagine `arr` as cells with fences between them:

```
|  0  |  1  |  2  |  3  |   ← indices
^                       ^
|                       |
len = 4, bounds are [0, 4)
```

`range(len)` hits every cell. `range(len - 1)` skips the last. `range(1, len)` skips the first. Subtract based on which fence you're about to fall through.

## Gotchas

**Gotcha 1 — pairwise is cleaner than `range(len-1)` + indexing.** If you're doing `arr[i], arr[i+1]`, prefer:
```python
from itertools import pairwise
for a, b in pairwise(arr): ...
```
No off-by-one risk, no indexing.

**Gotcha 2 — off-by-one is the MOST common LC bug.** When your solution "works on a small example but fails on edge cases," the `range` bound is a prime suspect. Trace the last iteration by hand.

**Gotcha 3 — empty / single-element inputs.** `range(len([]))` is `range(0)` — 0 iterations (safe). `range(len([5]) - 1)` is `range(0)` — 0 iterations (may skip the only element; check if that's intended).
