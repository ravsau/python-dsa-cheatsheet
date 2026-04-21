# Unpacking & swap

**TL;DR:** `a, b = b, a` swaps without a temp. `first, *rest` captures head+tail. Simultaneous assignment — RHS is snapshot *before* any LHS is bound.

**Patterns:** swap, rolling_state, pointer_rotation

## Where it matters — with usage

### LC 206 Reverse Linked List — three-way rotation, no temps
```python
def reverseList(head):
    prev = None
    curr = head
    while curr:
        curr.next, prev, curr = prev, curr, curr.next
        # RHS snapshot: (prev, curr, curr.next)
        # LHS assignment: curr.next ← old prev, prev ← old curr, curr ← old curr.next
    return prev
```

### LC 70 Climbing Stairs — Fibonacci rolling
```python
def climbStairs(n):
    a, b = 1, 1                           # ways(0), ways(1)
    for _ in range(n):
        a, b = b, a + b                   # roll forward
    return a
```

### LC 198 House Robber — 2-state DP without array
```python
def rob(nums):
    prev, curr = 0, 0
    for n in nums:
        prev, curr = curr, max(curr, prev + n)
        # curr = best if we include n (prev + n) vs skip (curr)
    return curr
```

### LC 24 Swap Nodes in Pairs — pointer rotation
```python
def swapPairs(head):
    dummy = ListNode(0, head)
    prev = dummy
    while prev.next and prev.next.next:
        a, b = prev.next, prev.next.next
        a.next, b.next, prev.next = b.next, a, b     # simultaneous rewire
        prev = a
    return dummy.next
```

### LC 344 Reverse String — in-place swap
```python
def reverseString(s):
    l, r = 0, len(s) - 1
    while l < r:
        s[l], s[r] = s[r], s[l]
        l, r = l + 1, r - 1               # (or l += 1; r -= 1)
```

### LC 189 Rotate Array — slice unpacking
```python
def rotate(nums, k):
    k %= len(nums)
    nums[:] = nums[-k:] + nums[:-k]       # mutate in place via slice-assign
```

### LC 88 Merge Sorted Array — unpack pairs in a loop
```python
def merge(nums1, m, nums2, n):
    i, j, k = m - 1, n - 1, m + n - 1
    while j >= 0:
        if i >= 0 and nums1[i] > nums2[j]:
            nums1[k] = nums1[i]; i -= 1
        else:
            nums1[k] = nums2[j]; j -= 1
        k -= 1
```

## Extended unpacking forms

```python
first, *rest = [1,2,3,4]           # first=1, rest=[2,3,4]
*init, last = [1,2,3,4]            # init=[1,2,3], last=4
first, *middle, last = [1,2,3,4,5] # first=1, middle=[2,3,4], last=5
a, b, *_ = [1,2,3,4,5]             # ignore the rest (convention: _)

# nested
for (a, b), c in [((1,2), 3), ((4,5), 6)]: ...
```

## Spread in function calls

```python
print(*nums)                  # print(1, 2, 3)
max(*arr)                     # unpack into positional args
list(zip(*matrix))            # transpose
func(**kwargs)                # unpack dict as kwargs
```

**Why simultaneous assignment is safe:** RHS is fully evaluated into a tuple FIRST, then LHS names are bound. So `a, b = b, a` needs no temp — Python snapshots `(b, a)` before any write.

**Gotcha 1 — too many / too few values.** `a, b = [1,2,3]` → ValueError. Use `a, b, *_ = ...` to absorb extras.

**Gotcha 2 — only one `*` allowed per LHS.** `a, *b, *c = arr` → SyntaxError.

**Gotcha 3 — for-loop targets are NOT parallel.** `for i, arr[i] in enumerate(...)` assigns `i` first, then uses the new `i` to index `arr[i]`. Rarely what you want.
