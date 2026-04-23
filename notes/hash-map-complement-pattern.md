# Hash-map complement pattern — check before store

**TL;DR:** For "find pair/subarray with target property" problems, iterate once and maintain a hash map of things-seen-so-far. **Check the complement FIRST, then store the current element.** Reversing the order makes you match elements against themselves.

**Patterns:** hash_map, complement_lookup, prefix_sum

## The core pattern

```python
seen = {}                              # or defaultdict(int) for counts
for i, x in enumerate(arr):
    complement = some_function(x)      # what we're looking for
    if complement in seen:             # ← CHECK FIRST (using previously-seen entries)
        return use(seen[complement], i, x)
    seen[x] = i                        # ← THEN STORE (this becomes "seen" for future iterations)
```

## Why order matters — the trap

Reversed order (store first, then check) lets you match an element with **itself**:

```python
# BAD — will match x with itself
seen[x] = i
if complement in seen:       # complement might BE x, now in seen at index i
    return ...
```

**Example for LC 1 Two Sum:** `nums = [3, 2, 4], target = 6`. If you store 3 at index 0, then check `target - 3 = 3` in seen — yes, 3 is in seen, at index 0. You return a pair (0, 0) — same element used twice. Wrong.

**Correct order:** check first (match against *previous* elements only), then store (so *future* elements can match with us).

## Where it matters — with usage

### LC 1 Two Sum — pair that sums to target
```python
def twoSum(nums, target):
    seen = {}
    for i, num in enumerate(nums):
        complement = target - num
        if complement in seen:
            return [seen[complement], i]   # return INDICES
        seen[num] = i
```

### LC 560 Subarray Sum Equals K — prefix sum with count
```python
def subarraySum(nums, k):
    count = 0
    prefix = 0
    seen = {0: 1}                          # {prefix_sum: number_of_times_seen}; {0: 1} seeds "empty prefix"
    for num in nums:
        prefix += num
        complement = prefix - k            # if we saw prefix-k before, that means current range sums to k
        if complement in seen:
            count += seen[complement]      # add all previous occurrences
        seen[prefix] = seen.get(prefix, 0) + 1   # store AFTER check
    return count
```

### LC 219 Contains Duplicate II — same number within distance k
```python
def containsNearbyDuplicate(nums, k):
    last_seen = {}
    for i, num in enumerate(nums):
        if num in last_seen and i - last_seen[num] <= k:
            return True
        last_seen[num] = i                 # always store the LATEST index
    return False
```

### LC 974 Subarray Sums Divisible by K — prefix sum mod k
```python
def subarraysDivByK(nums, k):
    count = 0
    prefix = 0
    seen = {0: 1}
    for num in nums:
        prefix = (prefix + num) % k
        if prefix in seen:
            count += seen[prefix]
        seen[prefix] = seen.get(prefix, 0) + 1
    return count
```

### LC 523 Continuous Subarray Sum — prefix mod k, index distance
```python
def checkSubarraySum(nums, k):
    seen = {0: -1}                         # seed with index -1 to handle sum-from-start
    prefix = 0
    for i, num in enumerate(nums):
        prefix = (prefix + num) % k
        if prefix in seen:
            if i - seen[prefix] >= 2:
                return True
        else:
            seen[prefix] = i              # only store FIRST occurrence (for max distance)
    return False
```

## Two variants of the pattern

### Variant A — store **latest** index (most common)
Use when "as recent as possible" gives the right answer (LC 1, LC 219).
```python
seen[x] = i                    # overwrite prior entry
```

### Variant B — store **earliest** index (store only if absent)
Use when maximum-distance / longest-span matters (LC 523, LC 325).
```python
if x not in seen:
    seen[x] = i                # keep the first occurrence
```

Don't mix these up — it's the difference between AC and WA.

## The "seed" trick (for prefix-sum problems)

Many prefix-sum variants seed `seen[0] = 1` (or `-1` for index). This represents the **empty prefix** — "if prefix hits the target, the subarray starts from index 0."

```python
seen = {0: 1}       # count variant (LC 560, 974)
seen = {0: -1}      # index variant (LC 523, LC 325)
```

Skip the seed and you silently miss subarrays that start at index 0.

## Gotchas

**Gotcha 1 — order of check-then-store.** The canonical bug. If you store before checking, you can match an element with itself. Always check first.

**Gotcha 2 — forgetting the seed on prefix-sum variants.** Subarrays that begin at index 0 require `seen = {0: ...}` to be matched. Without the seed they're silently skipped.

**Gotcha 3 — latest vs earliest matters.** `seen[x] = i` (overwrite) vs `seen.setdefault(x, i)` (keep first) are NOT interchangeable. Pick based on the problem semantics (shortest distance = latest; longest/first = earliest).

**Gotcha 4 — `in dict.keys()` is redundant.** `x in dict` already checks keys. Dropping `.keys()` is both faster and more idiomatic.

**Gotcha 5 — shadowing the built-in `dict`.** Naming your variable `dict` overrides the type for the rest of the function. Use `seen`, `lookup`, `index_map`, `prefix_count`.
