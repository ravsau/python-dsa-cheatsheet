# `[0] * n` — list repetition for fixed-size init

**TL;DR:** `[0] * n` creates a list of `n` zeros. The `*` is **list repetition**, not integer multiplication. Standard Python idiom for "fixed-size array prefilled with a default value." Safe for primitives (`0`, `False`, `None`); breaks for nested lists (see aliasing trap).

**Where it matters:**
- LC 70 Climbing Stairs — `dp = [0] * (n + 1)` (1D DP table)
- LC 198 House Robber — `dp = [0] * len(nums)` (DP table)
- LC 322 Coin Change — `dp = [float('inf')] * (amount + 1)` (DP with sentinel)
- LC 49 Group Anagrams — `count = [0] * 26` (alphabet frequency array)
- LC 200 Number of Islands — `[False] * len(grid)` (1D visited)
- Any fixed-size buffer init

**Patterns:** dp_1d, fixed_size_array, frequency_count, init

## The basic idiom

```python
[0] * 5      → [0, 0, 0, 0, 0]
[0] * 1      → [0]
[0] * 0      → []                # zero-length, valid
```

The `*` operator on a list **repeats the list** N times, concatenating into a new list.

## How `*` is overloaded by type

```python
0   * 5      → 0              # int multiplication
[0] * 5      → [0,0,0,0,0]    # list repetition
"a" * 5      → "aaaaa"        # string repetition
"-" * 20     → "--------------------"
(0,) * 5     → (0,0,0,0,0)    # tuple repetition
```

Common bug for new Python users: writing `0 * 5` (int 0) when you meant `[0] * 5` (list of zeros). The brackets matter.

## Common init recipes

```python
[0] * n              # n zeros — DP counts, sums, frequencies
[1] * n              # n ones — DP "ways" with implicit empty path
[False] * n          # boolean visited array
[None] * n           # n placeholders
[''] * n             # n empty strings
[float('inf')] * n   # min-DP sentinels (LC 322, LC 64)
[float('-inf')] * n  # max-DP sentinels
[-1] * n             # "not yet computed" marker for memoization

# 26-letter frequency array (lowercase a-z)
counts = [0] * 26
counts[ord(c) - ord('a')] += 1   # bump frequency
```

## ⚠️ The aliasing trap — only safe for IMMUTABLE values

```python
[0] * 3              # ✓ fine — integers are immutable
[False] * 3          # ✓ fine — booleans immutable
[''] * 3             # ✓ fine — strings immutable

[[0]*3] * 2          # ❌ ALIASING — both rows are the SAME list
                     #    mutating one mutates all
```

For nested lists, **always use a list comprehension**:

```python
[[0] * cols for _ in range(rows)]    # ✓ each row is a fresh list
```

See `2d-list-aliasing.md` for full details on this trap.

**Rule:** `[X] * n` is safe iff `X` is immutable. Numbers/strings/booleans/None: safe. Lists/dicts/sets: alias bug.

## Why this idiom over `list(range(...))` or comprehension

```python
# ✓ idiomatic, fastest
dp = [0] * n

# also works but slower / verbose
dp = [0 for _ in range(n)]              # list comprehension
dp = list(0 for _ in range(n))           # generator into list
```

`[0] * n` is implemented in C internally and is the fastest way to build a uniform array. Use the comprehension form only when each element needs to be **different** (e.g., `[i*2 for i in range(n)]`).

## DP-specific note — usually you allocate `n+1` slots, not `n`

For 1D DP, you commonly need to access `dp[n]` (the final answer). To make index `n` valid, the array needs `n+1` slots (Python is 0-indexed):

```python
dp = [0] * (n + 1)        # indices 0..n inclusive — fencepost rule
dp[n] = ...               # safe
```

If you wrote `dp = [0] * n`, valid indices would be `0..n-1`, and `dp[n]` would crash with `IndexError`. Same `+1` fencepost as window size and inclusive ranges.

## TL;DR (one line)

> **`[X] * n` creates a list of `n` copies of `X`. Safe for primitives (numbers, strings, booleans, None); aliases for mutable values like lists. For 1D DP, usually `[0] * (n+1)` to allow indexing up to `n` inclusive.**
