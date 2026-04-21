# Set algebra — `&`, `|`, `-`, `^`

**TL;DR:** Sets (and `dict.keys()`) support `&` `|` `-` `^`. One-liners for "in A but not B" style problems.

**Patterns:** set_membership, duplicate_detection

```python
a & b           # intersection
a | b           # union
a - b           # difference (in a, not in b)
a ^ b           # symmetric difference (in exactly one)
a <= b          # subset check
```

## Where it matters — with usage

### LC 349 Intersection of Two Arrays — one liner
```python
def intersection(nums1, nums2):
    return list(set(nums1) & set(nums2))         # unique intersection
```

### LC 350 Intersection of Two Arrays II — counts matter
```python
from collections import Counter
def intersect(nums1, nums2):
    c = Counter(nums1) & Counter(nums2)          # min of counts per key
    return list(c.elements())                     # repeats each by its count
```

### LC 202 Happy Number — cycle detection via `seen`
```python
def isHappy(n):
    seen = set()
    while n != 1 and n not in seen:
        seen.add(n)
        n = sum(int(c)**2 for c in str(n))
    return n == 1
```

### LC 128 Longest Consecutive Sequence — O(n) via set membership
```python
def longestConsecutive(nums):
    s = set(nums)
    best = 0
    for n in s:
        if n - 1 not in s:                       # start of a run (no smaller neighbor)
            cur = n
            while cur + 1 in s:                  # extend the run
                cur += 1
            best = max(best, cur - n + 1)
    return best
```

### LC 771 Jewels and Stones — membership sum
```python
def numJewelsInStones(jewels, stones):
    J = set(jewels)
    return sum(s in J for s in stones)           # bool → int
```

### LC 2215 Find the Difference of Two Arrays — set diffs
```python
def findDifference(nums1, nums2):
    a, b = set(nums1), set(nums2)
    return [list(a - b), list(b - a)]
```

### LC 217 Contains Duplicate — set length trick
```python
def containsDuplicate(nums):
    return len(nums) != len(set(nums))           # dedup shrinks it → duplicate existed
```

## Counter supports the same ops with multiplicity

```python
Counter("aab") & Counter("abc")  # Counter({'a':1, 'b':1})   — min of counts (intersect)
Counter("aab") | Counter("abc")  # Counter({'a':2, 'b':1, 'c':1})  — max of counts (union)
Counter("aab") - Counter("abc")  # Counter({'a':1})           — drops non-positive
Counter("aab") + Counter("abc")  # Counter({'a':3, 'b':2, 'c':1})  — multiset sum
```

**Gotcha 1 — `{}` is an empty dict, NOT an empty set.** Use `set()` for empty set. `{1, 2, 3}` is a set literal (non-empty).

**Gotcha 2 — sets are unordered.** For "unique elements in insertion order," use `list(dict.fromkeys(arr))` — dict preserves order, set doesn't.

**Gotcha 3 — elements must be hashable.** `set([[1,2], [3,4]])` → TypeError. Convert nested lists to tuples first: `set(tuple(row) for row in matrix)`.

**Gotcha 4 — regular `set` isn't hashable.** For a set-of-sets, use `frozenset`.
