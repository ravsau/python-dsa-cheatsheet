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

---

## Counter internals (when the interviewer asks)

Counter is a **dict subclass** (~10 lines of real logic). Here's the essence:

```python
class MyCounter(dict):
    def __init__(self, iterable=None):
        super().__init__()
        if iterable is not None:
            for item in iterable:
                self[item] = self.get(item, 0) + 1     # O(n) single pass

    def __missing__(self, key):
        return 0                                        # missing keys → 0, not KeyError

    def most_common(self, n=None):
        import heapq
        if n is None:
            return sorted(self.items(), key=lambda x: -x[1])        # O(k log k)
        return heapq.nlargest(n, self.items(), key=lambda x: x[1])  # O(k log n)
```

**What the real `Counter` adds on top:** `+`, `-`, `&`, `|` arithmetic for multiset operations; `.subtract()` allowing negatives; `.elements()` iterator.

**Why `Counter(s) == Counter(t)` works for anagram:** since Counter inherits from dict, `==` is dict equality — order-insensitive, compares key-value pairs. `{'a':2, 'b':1} == {'b':1, 'a':2}` is True.

**Counter vs defaultdict(int) vs manual dict:**

```python
# manual
d = {}
for c in s: d[c] = d.get(c, 0) + 1

# defaultdict
from collections import defaultdict
d = defaultdict(int)
for c in s: d[c] += 1

# Counter (preferred for frequency)
from collections import Counter
d = Counter(s)
```

All three are O(n) with the same asymptotic complexity. Counter wins on readability + built-in `.most_common` + arithmetic operators.

**Senior interview move:** after writing `Counter(s) == Counter(t)`, volunteer:
> "Counter is a dict subclass — it's O(n) to build, O(1) amortized per insert. Equality falls through to dict equality, which compares key-value pairs ignoring insertion order. If you'd prefer I skip the stdlib, I can use a 26-slot count array — same O(n) time, O(1) space since the alphabet is bounded."

One sentence → demonstrates knowledge of internals AND of the optimization ladder.
