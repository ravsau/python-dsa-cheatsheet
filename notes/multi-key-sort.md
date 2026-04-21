# Multi-key sort — tuple keys and negation

**TL;DR:** `sorted(arr, key=lambda x: (a, b, c))` sorts by `a` then `b` then `c`. Negate any numeric field to reverse just that one.

**Patterns:** sorting, tuple_key, custom_comparator

## Where it matters — with usage

### LC 56 Merge Intervals — sort by start
```python
def merge(intervals):
    intervals.sort(key=lambda x: x[0])               # single-field sort
    out = [intervals[0]]
    for s, e in intervals[1:]:
        if s <= out[-1][1]:
            out[-1][1] = max(out[-1][1], e)          # extend
        else:
            out.append([s, e])
    return out
```

### LC 253 Meeting Rooms II — sort by start, heap by end
```python
def minMeetingRooms(intervals):
    intervals.sort(key=lambda x: x[0])               # process in start order
    heap = []                                        # min-heap of end times
    for s, e in intervals:
        if heap and heap[0] <= s:
            heapq.heappop(heap)                      # reuse that room
        heapq.heappush(heap, e)
    return len(heap)
```

### LC 692 Top K Frequent Words — freq desc, word asc
```python
def topKFrequent(words, k):
    c = Counter(words)
    return sorted(c.keys(), key=lambda w: (-c[w], w))[:k]
    # -c[w] = frequency descending (can negate ints)
    # w      = alphabetical ascending (strings can't be negated — leave natural)
```

### LC 1636 Sort by Increasing Frequency — freq asc, value desc
```python
def frequencySort(nums):
    c = Counter(nums)
    return sorted(nums, key=lambda x: (c[x], -x))
    # c[x] asc primary, -x desc secondary (tie-break)
```

### LC 937 Reorder Log Files — type + content ordering
```python
def reorderLogFiles(logs):
    def key(log):
        _id, rest = log.split(" ", 1)
        is_digit = rest[0].isdigit()
        # letter-logs first (False < True), then by content, then by id
        return (is_digit, rest if not is_digit else "", _id if not is_digit else "")
    return sorted(logs, key=key)
```

### LC 791 Custom Sort String — index by reference
```python
def customSortString(order, s):
    rank = {c: i for i, c in enumerate(order)}
    return "".join(sorted(s, key=lambda c: rank.get(c, len(order))))
    # unmapped chars go to the end (rank = len(order))
```

### LC 179 Largest Number — tuple key FAILS, need cmp_to_key
```python
# see cmp-to-key-custom-compare.md — "a+b vs b+a" can't be expressed as key(a) alone
```

## Reversing specific fields

```python
# numeric fields: negate
key=lambda x: (-x[0], x[1])                   # field 0 desc, field 1 asc

# string fields: can't negate; sort twice (stable sort) or use cmp_to_key
arr.sort(key=lambda x: x[1])                  # secondary first
arr.sort(key=lambda x: x[0], reverse=True)    # primary last — stable preserves order
```

## Complexity

- Python uses Timsort — O(n log n), stable
- Key function runs once per element (n times), not n log n — "decorate-sort-undecorate"
- Negation in tuple key is O(1) per element

**Gotcha 1 — stability matters.** Sort by secondary first, primary last, so primary becomes the dominant order and primary ties preserve secondary ordering.

**Gotcha 2 — `reverse=True` applies to the WHOLE sort.** There's no per-field `reverse` — that's what negation is for.

**Gotcha 3 — can't negate strings.** `key=lambda x: -x[0]` on a string field → TypeError. Use multi-pass sort or `cmp_to_key`.
