# Counter.most_common(k)

**TL;DR:** `collections.Counter(iterable).most_common(k)` returns the `k` highest-frequency items as `[(val, count), ...]`. One line replaces manual frequency dict + sort.

**Where it matters:**
- LC 347 Top K Frequent Elements — literally `Counter(nums).most_common(k)` + unpack
- LC 451 Sort Characters By Frequency — `most_common()` without `k`
- LC 692 Top K Frequent Words — needs tiebreak on lexical order, so `most_common` isn't enough alone
- LC 1636 Sort Array by Increasing Frequency — sort by freq asc, then value desc
- LC 242 Valid Anagram — `Counter(s) == Counter(t)`

**Patterns:** frequency, top_k, hash_map

**Code:**
```python
from collections import Counter

c = Counter([1,1,1,2,2,3])           # Counter({1: 3, 2: 2, 3: 1})
c.most_common(2)                     # [(1, 3), (2, 2)]

# LC 347 in one line
[v for v, _ in Counter(nums).most_common(k)]

# anagram check
Counter(s) == Counter(t)             # True if anagrams
```

**Complexity:**
- `Counter(iterable)` — O(n)
- `most_common(k)` — O(n log k) via internal heap (better than `most_common()` without k, which is O(n log n))
- `most_common()` — O(n log n)

**Arithmetic on Counters:**
```python
Counter("aab") + Counter("abc")      # Counter({'a': 3, 'b': 2, 'c': 1})
Counter("aab") - Counter("abc")      # Counter({'a': 1})  — drops non-positive
Counter("aab") & Counter("abc")      # Counter({'a': 1, 'b': 1})  — intersection (min)
Counter("aab") | Counter("abc")      # Counter({'a': 2, 'b': 1, 'c': 1})  — union (max)
```

**Gotcha 1 — tiebreak order.** `most_common` preserves *insertion order* for ties (Python 3.7+ dict order). For lexical/numeric tiebreak (LC 692), sort manually: `sorted(c.items(), key=lambda x: (-x[1], x[0]))`.

**Gotcha 2 — negative counts.** Counter allows negatives (from subtraction without `-`), but `most_common` returns them in numeric order like any int — not always what you want.

**Gotcha 3 — missing keys return 0, not KeyError.** `Counter()["never_seen"] == 0`. Unlike a plain dict. Useful for difference checks without `defaultdict`.
