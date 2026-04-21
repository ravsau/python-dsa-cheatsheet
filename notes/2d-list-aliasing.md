# 2D list aliasing

**TL;DR:** `[[0]*cols]*rows` creates `rows` references to the **same** inner list. Mutating one row mutates all of them.

**Where it matters:**
- LC 62 Unique Paths — dp grid
- LC 63 Unique Paths II — dp grid
- LC 64 Minimum Path Sum — dp grid
- LC 72 Edit Distance — 2D dp
- LC 79 Word Search — visited grid
- LC 221 Maximal Square — dp grid
- *any* grid DP or visited-matrix problem

**Patterns:** dp_2d, grid_traversal, backtracking

**The bug:**
```python
dp = [[0]*3]*2
dp[0][0] = 5
print(dp)   # [[5, 0, 0], [5, 0, 0]]   ← both rows mutated
```

**The fix (always use comprehension for 2D):**
```python
dp = [[0]*3 for _ in range(2)]
dp[0][0] = 5
print(dp)   # [[5, 0, 0], [0, 0, 0]]   ✓
```

**Why:** `list * n` copies the reference n times (shallow). For primitives this is fine (`[0]*3 == [0,0,0]`, each `0` is immutable). For lists it aliases because the inner list is mutable.

**Complexity impact:** none on time/space, but introduces O(1) silent correctness bugs. Your DP table appears to "mysteriously" have every row identical.

**Gotcha:** Works accidentally if you only ever *read* from the grid, fails the moment you write. Bugs surface only mid-execution, hard to debug from a small example.
