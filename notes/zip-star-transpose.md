# zip(*matrix) — transpose in one line

**TL;DR:** `list(zip(*matrix))` transposes — rows become columns.

**Patterns:** matrix, grid_traversal

## Where it matters — with usage

### LC 48 Rotate Image — 90° clockwise = reverse rows + transpose
```python
def rotate(matrix):
    matrix[:] = [list(row) for row in zip(*matrix[::-1])]
    # step by step:
    # matrix[::-1]        flip vertically
    # zip(*...)           transpose
    # list(row) for row   convert tuples back to lists
```

### LC 867 Transpose Matrix — literally the problem
```python
def transpose(matrix):
    return [list(row) for row in zip(*matrix)]
```

### LC 36 Valid Sudoku — iterate columns cleanly
```python
def isValidSudoku(board):
    def has_dupes(group):
        seen = [c for c in group if c != '.']
        return len(seen) != len(set(seen))

    if any(has_dupes(row) for row in board): return False
    if any(has_dupes(col) for col in zip(*board)): return False   # columns!
    for r in range(0, 9, 3):
        for c in range(0, 9, 3):
            box = [board[r+i][c+j] for i in range(3) for j in range(3)]
            if has_dupes(box): return False
    return True
```

### LC 832 Flipping an Image — flip horizontally, then invert
```python
def flipAndInvertImage(image):
    return [[1 - p for p in row[::-1]] for row in image]
    # zip(*) not needed here — demonstrates alt matrix manipulation
```

### Parallel iteration (the non-transpose use of zip)
```python
# LC 88 Merge Sorted Array (in spirit)
for a, b in zip(arr1, arr2):              # pair by index
    ...

# LC 205 Isomorphic Strings — pair chars
for c1, c2 in zip(s, t):
    ...
```

### Unzip — splitting pairs
```python
pairs = [(1, 'a'), (2, 'b'), (3, 'c')]
nums, letters = zip(*pairs)                # (1, 2, 3), ('a', 'b', 'c')
```

## Complexity

- O(rows × cols) time + space when materialized as list
- O(1) to create the `zip` object (lazy)
- `zip(*m)` on an n×n matrix is fine for n ≤ 10^3

**Gotcha 1 — returns tuples, not lists.** Wrap: `[list(row) for row in zip(*m)]` if you need mutable rows.

**Gotcha 2 — jagged matrices silently truncate.** `zip([1,2,3], [4,5])` → `[(1,4),(2,5)]`, no error. For padded behavior use `itertools.zip_longest(..., fillvalue=None)`.

**Gotcha 3 — empty matrix.** `zip(*[])` is empty. Guard with `if not matrix: return []`.
