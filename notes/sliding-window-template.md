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

## The 3-beat rhythm (universal mantra)

> **Three pieces. Expand always. Shrink while [condition]. Record [somewhere].**

Every variable sliding window follows this exact shape:

```python
for right, x in enumerate(arr):
    # 1. EXPAND — unconditional. Always add the new right element to window state.
    state.add(x)
    
    # 2. SHRINK — conditional. Drive `left` forward while [condition] holds.
    while [condition]:
        # ... record might live HERE ...
        state.remove(arr[left])
        left += 1
    
    # ... or record might live HERE ...

return answer
```

**Three operations. Two of them are mechanical (expand always, shrink while …). The third (record) varies by flavor.**

If you can't immediately fill in `[condition]` and `[somewhere]`, you don't yet understand the problem — pause and answer the 4 questions below before writing code.

## The 4-question checklist (answer these BEFORE coding)

1. **Are you looking for LONGEST or SHORTEST?**
   - LONGEST → shrink while INVALID (fix the violation, then grow)
   - SHORTEST → shrink while VALID (try to find a smaller valid window)

2. **What does "valid" mean for this problem?**
   - LC 3: no duplicate in window
   - LC 209: `sum >= target`
   - LC 76: window contains all chars of `t` with right multiplicity
   - LC 424: at most `k` differing chars after replacement

3. **What state do you need?**
   - `set` → "is x in the window?" (LC 3)
   - running sum → sum-based problems (LC 209)
   - `Counter` / dict → frequency-based problems (LC 76, 424, 567)

4. **WHERE do you record the answer?**
   - LONGEST → AFTER the shrink (window is now valid; we want max size)
   - SHORTEST → INSIDE the while (every shrink iteration is a candidate)

## The two flavors — side-by-side

| | **LONGEST valid X** | **SHORTEST valid X** |
|---|---|---|
| Goal | maximize valid window | minimize valid window |
| Examples | LC 3, LC 424, LC 1004 | LC 209, LC 76 |
| Shrink condition | `while INVALID` (fix violation) | `while VALID` (try shorter) |
| Record at | AFTER while exits | INSIDE while (before subtract) |
| Sentinel for "not found" | `0` (no positive answer) | `float('inf')` or `len(arr)+1` |

**Same skeleton. Two different (condition, record-location) settings. That's it.**

## The shrink mechanic in plain English

> **"Keep evicting the leftmost char until my new char is no longer a duplicate of anything in the window. THEN it's safe to add the new char."**

That's the whole sliding-window shrink mechanic. Generalize "duplicate" → "constraint violation," and "char" → "element," and you have every variable-window problem.

The `while` shrinks one element at a time off the **left** edge, advancing `left` rightward, until the constraint is restored. Then — and only then — you accept the new right element and record the window.

**Three-beat rhythm per iteration:**
1. **See** new element on the right
2. **Shrink** from the left until valid (may take 0, 1, or many steps)
3. **Add** new element + **record** answer

You never go backward. Each element enters the window once (when `right` advances) and leaves once (when `left` advances past it) — that's why it's **O(n)** even though the inner `while` makes it look O(n²).

## Template — variable window (longest / shortest satisfying constraint)

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
