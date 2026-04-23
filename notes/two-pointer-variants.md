# Two-pointer variants — the family tree

**TL;DR:** "Two pointers" is an umbrella for four distinct patterns. Sliding window is one of them. Learn to name which variant a problem is asking for — that routes you to the right template instantly.

**Patterns:** two_pointers, sliding_window, fast_slow, read_write

## The four variants

| Variant | Pointer movement | Invariant maintained | Common cue |
|---|---|---|---|
| **Converging** | opposite → inward | sorted order / pair sum target | "sorted array", "palindrome", "pair that sums to..." |
| **Sliding window** | same direction, asynchronous | contiguous valid window | "longest/shortest subarray/substring with..." |
| **Fast & slow** | same direction, different speeds | gap or cycle | "cycle", "middle of linked list" |
| **Read / write (two-pass)** | same direction, one leads | in-place filter | "remove in place", "partition", "no extra space" |

## Where each applies — with named problems

### 1. Converging two pointers
```
indices:  0   1   2   3   4
              ←           →
              l←         →r      pointers collapse inward
```

- LC 15 3Sum — outer loop + converging pair
- LC 42 Trapping Rain Water — move the smaller-height pointer in
- LC 125 Valid Palindrome — check chars from both ends
- LC 167 Two Sum II (sorted) — converge by comparing sum vs target
- LC 11 Container With Most Water
- LC 344 Reverse String — swap from both ends

```python
# converging template
l, r = 0, len(arr) - 1
while l < r:
    if condition(arr[l], arr[r]):
        # do something
        l += 1
        r -= 1
    elif should_shrink_from_left:
        l += 1
    else:
        r -= 1
```

### 2. Sliding window
```
indices:  0   1   2   3   4
          l →         → r        both advance, window = [l..r]
          └── window ──┘
```

- LC 3 Longest Substring Without Repeating
- LC 76 Minimum Window Substring
- LC 209 Minimum Size Subarray Sum
- LC 239 Sliding Window Maximum — fixed-size + monotonic deque
- LC 424 Longest Repeating Char Replacement
- LC 567 Permutation in String

```python
# sliding window template (variable-size, expand-then-shrink)
l = 0
for r in range(len(s)):
    # expand: include s[r] in window state
    while violates(state):
        # shrink: remove s[l], advance l
        l += 1
    best = max(best, r - l + 1)       # (see inclusive-vs-exclusive-bounds.md)
```

### 3. Fast and slow pointers
```
index:   0 1 2 3 4 5 6 7
slow →   . →                          slow moves 1 step
fast →   . → →                        fast moves 2 steps
```

- LC 141 Linked List Cycle — fast catches slow iff cycle exists
- LC 142 Linked List Cycle II — find cycle start
- LC 876 Middle of Linked List — fast reaches end when slow hits middle
- LC 202 Happy Number — cycle detection on number seq
- LC 287 Find the Duplicate Number — Floyd's on array-as-function

```python
# fast & slow template
slow = fast = head
while fast and fast.next:
    slow = slow.next
    fast = fast.next.next
    if slow == fast:                   # cycle detected
        break
```

### 4. Read / write (two-pass in-place)
```
indices:  0   1   2   3   4
          w               r            w = next write position
                                       r = current read position
```

- LC 26 Remove Duplicates from Sorted Array
- LC 27 Remove Element
- LC 80 Remove Duplicates II (keep at most 2)
- LC 283 Move Zeroes — write nonzeros forward, pad zeros at end
- LC 88 Merge Sorted Array (reverse direction, trick variant)

```python
# read/write template
w = 0
for r in range(len(arr)):
    if keep(arr[r]):
        arr[w] = arr[r]
        w += 1
return w   # new length
```

## Decision flow — how to pick the variant mid-problem

```
Is the problem about CONTIGUOUS subarray/substring with a constraint?
├── YES → sliding window
└── NO → is the input sorted / about a pair-sum / palindrome?
        ├── YES → converging
        └── NO → is it a linked list / cycle / middle?
                ├── YES → fast & slow
                └── NO → is it "modify in place, no extra space"?
                        └── YES → read / write

If none fit → probably not a two-pointer problem; consider hash map, DP, or sort.
```

## Why seeing them as a family helps

Once the variants are named, "is this a two-pointer problem or a sliding-window problem?" stops being a real question. The real question is:

> **In which direction do the pointers move, and what invariant do they maintain?**

Answer that and the template follows.

## Gotchas

**Gotcha 1 — using converging template on an unsorted array.** Converging with pair-sum only works when the array is sorted. If not given sorted, either sort first (O(n log n) — may defeat the point) or switch to a hash-set approach.

**Gotcha 2 — sliding window on non-contiguous problems.** If the answer involves skipping elements, sliding window doesn't apply. Sliding window REQUIRES contiguous subarray/substring.

**Gotcha 3 — fast/slow doesn't always catch the cycle start.** Fast/slow DETECTS a cycle (they meet). Finding the cycle *start* requires a second phase (move one pointer to head, advance both at same speed — LC 142).

**Gotcha 4 — mixing read/write with modify in place.** If you're using `w` to track write position, don't also mutate `arr[r]` — you'll clobber unread data. Read-first, write-second, always.

## Related notes

- [`two-pointers-template.md`](two-pointers-template.md) — converging + read/write templates
- [`sliding-window-template.md`](sliding-window-template.md) — variable vs fixed window
- [`fast-slow-pointers.md`](fast-slow-pointers.md) — cycle detection specifics (coming soon)
- [`inclusive-vs-exclusive-bounds.md`](inclusive-vs-exclusive-bounds.md) — why `r - l + 1` in sliding window
