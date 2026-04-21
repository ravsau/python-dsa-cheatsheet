# Python gotchas that eat interviews

**TL;DR:** These bugs pass small test cases silently, then fail submission. Each has cost someone a screen. Know them cold.

## 1. `sort()` returns None

```python
arr = arr.sort()         # WIPES arr to None
arr.sort()                # correct — mutates in place
arr = sorted(arr)         # correct — returns new list
```

**Where it bites:** literally any sorting step. Submitted-then-crashed bug.

## 2. `[[0]*n]*m` aliasing

```python
dp = [[0]*3]*2            # WRONG — rows are aliased
dp[0][0] = 1
# dp == [[1,0,0], [1,0,0]]  — both "rows" mutated

dp = [[0]*3 for _ in range(2)]   # CORRECT
```

**Where it bites:** LC 62, 63, 64, 79, 200, 221 — any DP grid or visited matrix. See `2d-list-aliasing.md`.

## 3. `list.pop(0)` is O(n)

```python
q = [start]
while q:
    node = q.pop(0)       # O(n) shift — BFS becomes O(n²)

from collections import deque
q = deque([start])
node = q.popleft()        # O(1)
```

**Where it bites:** every BFS. LC 102, 127, 200, 994. See `list-pop-zero-is-on.md`.

## 4. Negative integer division

```python
-7 // 2                   # -4, not -3 (floors toward -inf)
-7 % 2                    #  1, not -1 (remainder follows divisor sign)
```

**Where it bites:** LC 7, 29, 166 — digit extraction on negatives. See `integer-division-with-negatives.md`.

## 5. Mutable default arg

```python
def dfs(node, path=[]):      # path persists across calls!
    path.append(node)
    ...

def dfs(node, path=None):    # CORRECT
    if path is None: path = []
    path.append(node)
```

**Where it bites:** backtracking problems (LC 46, 78, 39) where state leaks between test cases.

## 6. Shallow vs deep copy

```python
new = old                    # reference — mutations affect both
new = old[:]                 # shallow copy — top level copied, nested refs shared
new = old.copy()             # same as [:]
new = list(old)              # same
import copy
new = copy.deepcopy(old)     # full recursive copy

# matrix example:
m2 = matrix[:]               # WRONG for 2D — rows still shared
m2 = [row[:] for row in m]   # correct for 2D
```

**Where it bites:** LC 200 (visited grid passed by ref), LC 79 (backtracking with grid state).

## 7. Chained comparison (safe and underused)

```python
if 1 <= x <= 10:             # Pythonic — evaluates x ONCE
    ...
if x >= 1 and x <= 10:       # verbose, same result
    ...
```

**Where it bites:** not a bug, a missed-opportunity signal. Interviewer notices either way you write it.

## 8. `range` is lazy, not a list

```python
range(10) + range(5)          # TypeError — can't concat ranges
list(range(10)) + list(range(5))  # correct

range(10**18)                 # O(1) — no materialization
len(range(10**18))            # also O(1)
```

## 9. `is` vs `==`

```python
a = 256; b = 256              # a is b → True (int cache for -5..256)
a = 257; b = 257              # a is b → False (new object)
# rule: use `==` for value equality, `is` only for None/True/False/singletons
```

**Where it bites:** `if x is 0` works on small ints then breaks on large. Always `if x == 0`.

## 10. Dict ordering (3.7+)

```python
# Py 3.7+: dict preserves insertion order
# Py 3.6: CPython impl detail only, not guaranteed
# Py <= 3.5: arbitrary order

# sets do NOT preserve order, ever.
```

**Where it bites:** LC 146 (LRU) — relying on plain dict order works on modern Python but don't assume it for eviction — use `OrderedDict.popitem(last=False)`. See `ordered-dict-lru.md`.

## 11. Float comparison

```python
0.1 + 0.2 == 0.3              # False — 0.1 + 0.2 is 0.30000000000000004
abs(a - b) < 1e-9             # correct way
```

**Where it bites:** rarely in LC (most problems use ints), but any geometry/physics problem (LC 149 points on line).

## 12. Recursion limit

```python
import sys
sys.setrecursionlimit(10**4)   # default is ~1000
# for deep DFS / tree problems
```

**Where it bites:** LC 124 (max path sum on deep tree), any linked-list recursion on long lists.

## 13. `str` vs `bytes`

```python
b"abc"[0]                      # 97 (int)
"abc"[0]                       # 'a' (str)
```

**Where it bites:** rare on LC, common in file/encoding problems.

---

## Principle

If your code **works on small examples but fails submission**, scan this list first. 80% of "mystery failures" are one of these.
