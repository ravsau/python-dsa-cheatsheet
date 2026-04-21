# Sliding window — template

**TL;DR:** Maintain a contiguous `[left, right]` window, expand right, shrink left when the window violates a constraint. O(n) because each element enters + exits the window at most once.

**Where it matters:**
- LC 3 Longest Substring Without Repeating — variable window, shrink on duplicate
- LC 76 Minimum Window Substring — variable, shrink while valid
- LC 209 Minimum Size Subarray Sum — variable, shrink while sum ≥ target
- LC 239 Sliding Window Maximum — fixed window, monotonic deque
- LC 424 Longest Repeating Char Replacement — variable, k swaps allowed
- LC 567 Permutation in String — fixed window, frequency match
- LC 438 Find All Anagrams — fixed window, frequency match

**Patterns:** sliding_window, variable_window, fixed_window, monotonic_deque

**Complexity:** O(n) time, O(k) space for window state (`k` = alphabet size / window size).

**Template — variable window (longest / shortest satisfying constraint):**
```python
left = 0
state = ...                 # counter, sum, dict — whatever tracks validity
best = 0

for right in range(len(s)):
    # 1. expand: include s[right] in state
    state.add(s[right])

    # 2. shrink while invalid
    while violates(state):
        state.remove(s[left])
        left += 1

    # 3. record answer on every valid window
    best = max(best, right - left + 1)

return best
```

**Template — fixed window of size k:**
```python
for right in range(len(arr)):
    state.add(arr[right])
    if right >= k:
        state.remove(arr[right - k])
    if right >= k - 1:
        # window is [right-k+1 .. right], length k — record
        ...
```

**Keyword-to-template map:**
- "longest substring / subarray with…" → variable, expand+shrink
- "shortest substring / subarray such that…" → variable, shrink-while-valid
- "window of size k…" → fixed
- "contains all chars of t…" → variable, shrink once valid
- "max/min in every window of size k" → fixed + monotonic deque

**Gotcha 1 — `while` vs `if` for shrinking.** Shrinking is `while violates(...)`, not `if`. One step may not restore validity.

**Gotcha 2 — order of operations.** Update state *before* checking validity; update answer *after* restoring validity.

**Gotcha 3 — "exactly k" problems** → compute "at most k" − "at most k-1" (LC 992, 1248). Sliding window natively handles "at most", not "exactly".
