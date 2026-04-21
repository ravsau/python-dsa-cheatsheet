# any() / all() — short-circuit boolean reduction

**TL;DR:** `any(iterable)` returns True on first truthy. `all(iterable)` returns False on first falsy. Both short-circuit.

**Patterns:** boolean_reduction, early_exit

## Where it matters — with usage

### LC 896 Monotonic Array — cleanest form
```python
from itertools import pairwise
def isMonotonic(nums):
    return (all(a <= b for a, b in pairwise(nums))     # non-decreasing
         or all(a >= b for a, b in pairwise(nums)))    # non-increasing
```

### LC 217 Contains Duplicate — adjacent check on sorted
```python
def containsDuplicate(nums):
    return any(a == b for a, b in pairwise(sorted(nums)))
    # O(n log n) — vs the O(n) set approach: len(nums) != len(set(nums))
```

### LC 36 Valid Sudoku — rows, cols, boxes all valid
```python
def isValidSudoku(board):
    def unique(group):
        nums = [c for c in group if c != '.']
        return len(nums) == len(set(nums))

    return (all(unique(row) for row in board)
         and all(unique(col) for col in zip(*board))
         and all(unique(board[r+dr][c+dc] for dr in range(3) for dc in range(3))
                 for r in range(0, 9, 3) for c in range(0, 9, 3)))
```

### LC 79 Word Search — any starting cell works
```python
def exist(board, word):
    rows, cols = len(board), len(board[0])
    def dfs(r, c, i):
        if i == len(word): return True
        if not (0 <= r < rows and 0 <= c < cols) or board[r][c] != word[i]:
            return False
        board[r][c] = '#'                          # temp-mark visited
        found = any(dfs(r+dr, c+dc, i+1)
                    for dr, dc in [(-1,0),(1,0),(0,-1),(0,1)])
        board[r][c] = word[i]                      # restore
        return found
    return any(dfs(r, c, 0) for r in range(rows) for c in range(cols))
```

### LC 766 Toeplitz Matrix — adjacent-diagonal equality
```python
def isToeplitzMatrix(matrix):
    return all(matrix[r][c] == matrix[r-1][c-1]
               for r in range(1, len(matrix))
               for c in range(1, len(matrix[0])))
```

## Flag-loop elimination pattern

```python
# before — 4 lines + flag
found = False
for x in arr:
    if x == target:
        found = True
        break
if found: ...

# after — 1 line
if any(x == target for x in arr): ...
```

**Empty iterable semantics:**
```python
all([])     # True  (vacuous)
any([])     # False
```

**Gotcha 1 — `all([])` is True.** "No counterexamples" = satisfied. If you need "non-empty AND all-pass", guard: `arr and all(cond(x) for x in arr)`.

**Gotcha 2 — list comp kills short-circuit.** `any([f(x) for x in arr])` evaluates EVERY element before `any` runs. Use a generator: `any(f(x) for x in arr)`.

**Gotcha 3 — truthiness.** `any([0, None, "", []])` is False — all falsy. Rarely a bug, but if you need `is True`, compare explicitly.
