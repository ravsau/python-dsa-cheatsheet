# reversed() vs [::-1]

**TL;DR:** `reversed(arr)` is a lazy iterator — O(1) memory. `arr[::-1]` builds a new list — O(n) memory. Pick based on whether you need the reversed *object* or just reversed iteration.

**Patterns:** reverse_iteration, palindrome_check

## Where it matters — with usage

### LC 125 Valid Palindrome — need the reversed object for `==`
```python
def isPalindrome(s):
    cleaned = "".join(c.lower() for c in s if c.isalnum())
    return cleaned == cleaned[::-1]
    # reversed(cleaned) would be an iterator, can't ==
```

### LC 9 Palindrome Number — iterate backward without allocating
```python
def isPalindrome(x):
    if x < 0: return False
    s = str(x)
    for a, b in zip(s, reversed(s)):     # lazy, O(1) extra memory
        if a != b: return False
    return True
```

### LC 42 Trapping Rain Water — right-to-left pass for max_right
```python
def trap(height):
    n = len(height)
    left_max = [0] * n
    right_max = [0] * n
    left_max[0] = height[0]
    for i in range(1, n):
        left_max[i] = max(left_max[i-1], height[i])

    right_max[-1] = height[-1]
    for i in range(n-2, -1, -1):         # index matters — can't use reversed()
        right_max[i] = max(right_max[i+1], height[i])

    return sum(min(left_max[i], right_max[i]) - height[i] for i in range(n))
```

### LC 238 Product of Array Except Self — two passes
```python
def productExceptSelf(nums):
    n = len(nums)
    out = [1] * n
    for i in range(1, n):
        out[i] = out[i-1] * nums[i-1]        # left product
    right = 1
    for i in reversed(range(n)):              # right-to-left, no index math
        out[i] *= right
        right *= nums[i]
    return out
```

### LC 206 Reverse Linked List — need the reversed linked structure, not iteration
```python
# reversed() on a linked list doesn't exist — you must actually rewire pointers
# (see unpacking-and-swap.md for the classic idiom)
```

### LC 151 Reverse Words — want the reversed list, then join
```python
def reverseWords(s):
    return " ".join(s.split()[::-1])
    # " ".join(reversed(s.split())) also works — same result, different object type
```

## Decision table

| Need | Use |
|---|---|
| Iterate backwards once, don't need the object | `reversed(arr)` |
| Compare whole sequence to reversed (palindrome) | `arr[::-1]` |
| Pass reversed list to function expecting a list | `arr[::-1]` or `list(reversed(arr))` |
| Reverse a string | `s[::-1]` (no `str.reverse()` exists) |
| Reverse in place, no new object | `arr.reverse()` (returns None, mutates) |

**Gotcha 1 — `reversed()` on a generator fails.** Need `__reversed__` or `__len__ + __getitem__`. Lists, tuples, strings, ranges all support it.

**Gotcha 2 — palindrome via `reversed` is wrong.** `s == reversed(s)` is always False — iterator vs string. Use `s == s[::-1]`.

**Gotcha 3 — `enumerate(reversed(arr))`.** `i` counts forward over the reversed iteration. For original index, compute `len(arr) - 1 - i`.
