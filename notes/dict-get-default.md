# dict.get(key, default) and setdefault

**TL;DR:** `d.get(k, default)` returns `default` if missing. `d.setdefault(k, default)` initializes + returns. One-off defaults without promoting to `defaultdict`.

**Patterns:** frequency, hash_map

## Where it matters — with usage

### LC 387 First Unique Character — freq count without defaultdict
```python
def firstUniqChar(s):
    count = {}
    for c in s:
        count[c] = count.get(c, 0) + 1              # missing → 0, then +1
    for i, c in enumerate(s):
        if count[c] == 1:
            return i
    return -1
```

### LC 219 Contains Duplicate II — last-seen map
```python
def containsNearbyDuplicate(nums, k):
    last = {}
    for i, n in enumerate(nums):
        if i - last.get(n, -10**9) <= k:            # default to "very old" if unseen
            return True
        last[n] = i
    return False
```

### LC 49 Group Anagrams (without defaultdict) — setdefault + append
```python
def groupAnagrams(strs):
    groups = {}
    for s in strs:
        key = "".join(sorted(s))
        groups.setdefault(key, []).append(s)        # init list if needed, then append
    return list(groups.values())
```

### LC 205 Isomorphic Strings — mapping check
```python
def isIsomorphic(s, t):
    s_to_t, t_to_s = {}, {}
    for a, b in zip(s, t):
        if s_to_t.setdefault(a, b) != b: return False   # first sighting OR consistent
        if t_to_s.setdefault(b, a) != a: return False
    return True
```

### LC 1 Two Sum — membership + value lookup
```python
def twoSum(nums, target):
    seen = {}
    for i, num in enumerate(nums):
        j = seen.get(target - num)                  # None if not there
        if j is not None:
            return [j, i]
        seen[num] = i
```

## When to pick which

| Situation | Use |
|---|---|
| Occasional default in mostly-populated dict | `.get(k, default)` |
| Need to append/extend to a value you may not have created | `.setdefault(k, factory())` |
| Every access needs a default | `defaultdict(factory)` |

**Gotcha 1 — `.get()` does NOT insert.** Returns default but doesn't store it. `setdefault` DOES insert.

**Gotcha 2 — default is evaluated eagerly.** `d.get(k, expensive())` calls `expensive()` every time. If default is costly, check membership first.

**Gotcha 3 — can't tell "missing" from "present but None".** `.get()` returns None for both. Use `k in d` to disambiguate.
