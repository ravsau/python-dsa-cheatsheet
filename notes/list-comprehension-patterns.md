# List comprehension — the three shapes you need

**TL;DR:** Comprehensions replace 3-4 line loops with one expression. Three canonical shapes: filter, 2D grid, flatten.

**Where it matters:**
- LC 62 Unique Paths — `dp = [[1]*n for _ in range(m)]` (2D grid, CRITICAL)
- LC 200 Number of Islands — `visited = [[False]*cols for _ in range(rows)]`
- LC 54 Spiral Matrix — flatten + walk
- LC 1 Two Sum — `{num: i for i, num in enumerate(nums)}` (dict comp)
- LC 448 Find All Numbers Disappeared — `[i for i in range(1, n+1) if ...]`
- LC 771 Jewels and Stones — `sum(1 for c in stones if c in jewels)`

**Patterns:** initialization, filter, transform, dict_build

**The three shapes:**
```python
# 1. Filter
[x for x in arr if x > 0]

# 2. 2D grid (CRITICAL — NOT [[0]*n]*m)
[[0]*cols for _ in range(rows)]

# 3. Flatten
[item for row in matrix for item in row]
```

**Dict and set comps (same shape, different brackets):**
```python
{num: i for i, num in enumerate(arr)}          # dict
{x**2 for x in arr}                            # set
(x**2 for x in arr)                            # GENERATOR — lazy, parens not brackets
```

**Nested / with condition:**
```python
# transform + filter
[x.upper() for x in words if len(x) > 3]

# nested loops (ORDER: outer first, inner last)
[(i, j) for i in range(3) for j in range(3) if i != j]

# flatten with condition
[item for row in matrix for item in row if item > 0]
```

**Multiple outputs:**
```python
# can't return 2 lists from one comp; use zip(*...) pattern
pairs = [(x, x**2) for x in range(5)]
xs, squares = zip(*pairs)                      # ((0,1,...), (0,1,4,...))
```

**When NOT to use a comprehension:**
- Multiple side effects (print, mutate) — use a loop
- Deeply nested conditions — hurts readability
- Need `break` — comps can't break; use a loop or `next()` with generator
- Very long transform logic — extract a named function first

**Complexity:** same as equivalent loop — O(n) for linear, O(n × m) for 2D. Comprehensions are ~30% **faster** than loops due to C-level iteration.

**Gotcha 1 — `[[0]*n]*m` is ALIASED.** Every row references the same list. Mutating one row mutates all. Always use `[[0]*n for _ in range(m)]`. See `2d-list-aliasing.md`.

**Gotcha 2 — nested loop order.** `[x for row in mat for x in row]` — **outer-first**, same as nested `for`. Reversing the order reverses the iteration.

**Gotcha 3 — walrus inside comp leaks the name.** `[y for x in data if (y := f(x))]` — `y` is accessible after the comp. Usually harmless but can shadow other variables.
