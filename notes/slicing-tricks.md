# Slicing tricks

**TL;DR:** `arr[start:stop:step]`. Slicing returns a **copy** (lenient — no IndexError). Use `arr[:] = ...` to mutate in place.

**Patterns:** reversal, rotation, slice_copy

```python
arr[a:b]       # a to b-1
arr[:b]        # start to b-1
arr[a:]        # a to end
arr[:]         # full copy
arr[-k:]       # last k
arr[:-k]       # all except last k
arr[::2]       # every other
arr[::-1]      # reversed
```

## Where it matters — with usage

### LC 125 Valid Palindrome — one-line palindrome check
```python
def isPalindrome(s):
    cleaned = "".join(c.lower() for c in s if c.isalnum())
    return cleaned == cleaned[::-1]        # compare with reversed copy
```

### LC 189 Rotate Array — in-place rotation via slice-assign
```python
def rotate(nums, k):
    k %= len(nums)
    nums[:] = nums[-k:] + nums[:-k]
    # nums[-k:]  = last k elements
    # nums[:-k]  = everything before that
    # nums[:] =   = in-place mutation (caller sees change)
```

### LC 151 Reverse Words in a String
```python
def reverseWords(s):
    return " ".join(s.split()[::-1])
    # s.split()   → list of words (drops empty splits from multi-space)
    # [::-1]      → reverse the list
    # " ".join    → rejoin with single space
```

### LC 344 Reverse String — in-place via slice
```python
def reverseString(s):
    s[:] = s[::-1]               # mutates the caller's list
```

### LC 119 Pascal's Triangle II — rolling row
```python
def getRow(n):
    row = [1]
    for _ in range(n):
        row = [a + b for a, b in zip([0] + row, row + [0])]
    return row
# demonstrates pad-then-zip trick using list + slicing semantics
```

### LC 1913 Maximum Product Difference — sort + slice ends
```python
def maxProductDifference(nums):
    nums.sort()
    return nums[-1]*nums[-2] - nums[0]*nums[1]
    # two smallest × two largest
```

## In-place vs rebind — the critical distinction

```python
def f(arr):
    arr = sorted(arr)          # rebinds LOCAL name — caller's list UNCHANGED
    # ...

def f(arr):
    arr[:] = sorted(arr)       # mutates the list object — caller SEES change
    # or arr.sort() — same effect
```

Matters in LC 189, LC 283, LC 27 — anything saying "modify in place."

## Safe out-of-bounds

```python
arr = [1, 2, 3]
arr[100:200]     # []   — no IndexError, just empty slice
arr[1:100]       # [2, 3]
arr[-100]        # IndexError — INDEXING is strict, SLICING is lenient
```

## Shallow copy via slice

```python
shallow = arr[:]          # new outer list
shallow is arr            # False
shallow == arr            # True

# for nested lists, slice is STILL shallow — inner rows are shared
m2 = matrix[:]                        # outer copied, inner rows aliased
m2 = [row[:] for row in matrix]       # fully shallow per-row (works for 2D of primitives)
import copy
m2 = copy.deepcopy(matrix)            # fully deep
```

**Complexity:** slicing is O(k) where k = slice length (it copies). Loops that repeatedly slice can silently blow to O(n²).

**Gotcha 1 — slice is a copy, not a view.** Unlike NumPy. `arr[1:3][0] = 99` changes the temporary slice, not `arr[1]`.

**Gotcha 2 — negative step with wrong bounds.** `arr[2:5:-1]` is `[]` (can't go forward with negative step). For reverse from index 5 down to 2: `arr[5:1:-1]` (note 1 is exclusive, giving indices 5,4,3,2).

**Gotcha 3 — slicing a generator fails.** Generators aren't subscriptable. `list(gen)[::-1]` or `list(reversed(list(gen)))`.
