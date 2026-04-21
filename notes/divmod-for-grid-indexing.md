# divmod for grid indexing

**TL;DR:** `divmod(i, cols)` returns `(row, col)` — one-line conversion from flat index `i` to 2D coordinates in a `rows × cols` grid.

**Where it matters:**
- LC 54 Spiral Matrix — position tracking
- LC 59 Spiral Matrix II — same
- LC 289 Game of Life — neighbor enumeration on 1D-encoded state
- LC 36 Valid Sudoku — box index = `3 * (r // 3) + (c // 3)`
- any problem using a 1D `visited[]` to represent a 2D grid (memory savings)
- any BFS/DFS on a grid where you encode `(r, c)` as `r * cols + c` for hashing

**Patterns:** matrix, grid_traversal, coordinate_encoding

**Code:**
```python
row, col = divmod(i, cols)        # i → (row, col)
i = row * cols + col              # (row, col) → i
```

**Why use encoding:**
```python
# cleaner than set of tuples
visited = set()
visited.add(r * cols + c)         # int hash is faster than tuple hash

# and cleaner BFS state
q = deque([r0 * cols + c0])
while q:
    r, c = divmod(q.popleft(), cols)
    for dr, dc in [(-1,0),(1,0),(0,-1),(0,1)]:
        nr, nc = r+dr, c+dc
        if 0 <= nr < rows and 0 <= nc < cols:
            q.append(nr * cols + nc)
```

**Also handy:**
```python
hours, minutes = divmod(total_minutes, 60)      # LC 539 time-diff
quotient, remainder = divmod(n, 10)             # digit extraction
```

**Complexity impact:** none — purely ergonomic. But `int` hashing is ~2x faster than `tuple` hashing, so encoding `(r, c) → i` can be a real perf win in large grids.

**Gotcha:** encoding breaks when `cols` is variable per row (jagged grids). Stick to tuples there.
