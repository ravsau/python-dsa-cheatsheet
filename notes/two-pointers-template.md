# Two pointers — template

**TL;DR:** Two indices move through a structure, usually converging. Turns O(n²) brute force into O(n).

**Where it matters:**
- LC 11 Container With Most Water — converging from ends
- LC 15 3Sum — outer loop + converging pair
- LC 42 Trapping Rain Water — converging, move smaller side
- LC 125 Valid Palindrome — converging, skip non-alphanumeric
- LC 167 Two Sum II (sorted) — converging
- LC 344 Reverse String — converging, swap
- LC 26 Remove Duplicates — same-direction (slow/fast write-pointer)
- LC 283 Move Zeroes — same-direction

**Patterns:** two_pointers, two_pointers_converging, two_pointers_same_direction

**Complexity:** O(n) time, O(1) extra space (vs O(n²) nested loops).

**Template — converging (sorted/palindrome/pair-sum):**
```python
left, right = 0, len(arr) - 1
while left < right:
    # inspect arr[left], arr[right]
    if condition_move_left:
        left += 1
    elif condition_move_right:
        right -= 1
    else:
        # found / record / both move
        left += 1
        right -= 1
```

**Template — same-direction (in-place filter / partition):**
```python
slow = 0
for fast in range(len(arr)):
    if keep(arr[fast]):
        arr[slow] = arr[fast]
        slow += 1
return slow   # new length
```

**When to reach for it (keywords):**
- "sorted array" + "pair / triplet sum" → converging
- "palindrome" → converging from ends
- "remove in place" / "partition" → same-direction
- "without extra space" → almost always two-pointers or in-place swap

**Gotcha 1 — loop variables must advance.** Every branch of the `if/elif` must either move `left` or `right`. Miss one → infinite loop.

**Gotcha 2 — array must be sorted** for converging pair-sum variants. If not given sorted, sort first (costs O(n log n), still beats O(n²) hash set on space).

**Gotcha 3 — 3Sum needs dedup on *both* the outer `i` and inner `left` after a match,** else you get duplicate triplets.
