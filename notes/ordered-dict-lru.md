# OrderedDict — LRU cache in 15 lines

**TL;DR:** `OrderedDict` supports `move_to_end(key)` and `popitem(last=False)`, making LRU Cache a dict with two extra calls — no manual doubly-linked list.

**Where it matters:**
- LC 146 LRU Cache — the named problem; 15 lines vs 60 for linked-list version
- LC 460 LFU Cache — one `OrderedDict` per frequency bucket
- LC 1429 First Unique Number — maintain insertion order, pop from front
- LC 432 All O`one — order-preserving buckets
- LC 362 Design Hit Counter — time-ordered dict

**Patterns:** cache, order_preservation

**LC 146 LRU Cache — full solution:**
```python
from collections import OrderedDict

class LRUCache:
    def __init__(self, capacity: int):
        self.cap = capacity
        self.cache = OrderedDict()

    def get(self, key: int) -> int:
        if key not in self.cache:
            return -1
        self.cache.move_to_end(key)        # mark as recently used
        return self.cache[key]

    def put(self, key: int, value: int) -> None:
        if key in self.cache:
            self.cache.move_to_end(key)
        self.cache[key] = value
        if len(self.cache) > self.cap:
            self.cache.popitem(last=False) # evict LRU (oldest insertion)
```

**Key ops:**
```python
od.move_to_end(key)            # move to end = mark most-recent
od.move_to_end(key, last=False) # move to front
od.popitem()                   # pop (key, val) from end (last=True default)
od.popitem(last=False)         # pop from front = evict oldest
```

**Complexity:** all ops O(1). `OrderedDict` is a dict + doubly-linked list under the hood — same data structure an interviewer expects you to describe, just pre-built.

**When to hand-roll the DLL version instead:**
- Interviewer explicitly asks "don't use built-ins"
- You need **custom eviction** (LFU, LRU-K, time-aware TTL)
- You need **iteration during modification** with precise semantics

**The hand-rolled version (for when asked):**
```python
class Node:
    __slots__ = 'key', 'val', 'prev', 'next'
    def __init__(self, k=0, v=0):
        self.key, self.val = k, v
        self.prev = self.next = None
# + head/tail sentinels + add/remove helpers
```
Know how to write it from scratch as a fallback — some interviewers penalize `OrderedDict` even though it's the canonical stdlib answer.

**Gotcha 1 — regular `dict` preserves insertion order (Py 3.7+) but lacks `move_to_end`.** You NEED `OrderedDict` for LRU ops; a plain dict won't cut it.

**Gotcha 2 — `popitem(last=False)` is the eviction direction.** Missing `last=False` evicts the *newest*, which is the opposite of LRU.

**Gotcha 3 — `move_to_end` is O(1), not O(n).** People assume list-like O(n); it's doubly-linked under the hood.
