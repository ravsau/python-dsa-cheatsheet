# `sorted()` vs `list.sort()` — Python sorting cheatsheet

**TL;DR:** Python has TWO sorting tools. `sorted(iterable)` is a free function that works on ANY iterable and returns a NEW LIST. `list.sort()` is a list method that mutates in-place and returns `None`. Most other types (strings, tuples, dicts, sets) DON'T have `.sort()` — use `sorted()`.

**Where it matters:**
- LC 49 Group Anagrams — sort string chars (`''.join(sorted(s))` for hashable key)
- LC 56 Merge Intervals — sort by start time
- LC 215 Kth Largest — sort + index, or use heap
- LC 791 Custom Sort String — sort by custom comparator
- Any problem requiring ordered processing

**Patterns:** sorting, mutation_vs_value, in_place_ops

## The two tools

### `sorted(iterable, *, key=None, reverse=False)` — free function

```python
sorted([3, 1, 2])               # → [1, 2, 3]   (NEW list, original unchanged)
sorted("banana")                 # → ['a', 'a', 'a', 'b', 'n', 'n']  (list of chars!)
sorted((5, 2, 8))                # → [2, 5, 8]
sorted({3, 1, 2})                # → [1, 2, 3]
sorted({1: 'a', 3: 'b'})         # → [1, 3]   (sorts by KEYS)
sorted([3,1,2], key=lambda x: -x)  # → [3, 2, 1]  (custom key)
sorted([3,1,2], reverse=True)    # → [3, 2, 1]  (descending)
```

**Always returns a NEW LIST.** Original iterable untouched.

### `list.sort(*, key=None, reverse=False)` — instance method on lists

```python
arr = [3, 1, 2]
arr.sort()                       # mutates arr in-place → [1, 2, 3]
                                 # returns None
arr.sort(reverse=True)           # → [3, 2, 1]
arr.sort(key=lambda x: -x)       # custom key
```

**Mutates in-place. Returns None.** Don't assign the return value.

```python
result = arr.sort()              # ❌ result is None
arr = arr.sort()                 # ❌ arr is now None! (gotcha)
```

## Cheat table — when to use which

| You have | You want | Use |
|---|---|---|
| List, can mutate | sorted in-place, save space | `arr.sort()` |
| List, keep original | new sorted list | `sorted(arr)` |
| String | sorted STRING | `''.join(sorted(s))` |
| String | list of sorted chars | `sorted(s)` |
| Tuple | new sorted tuple | `tuple(sorted(t))` |
| Tuple | list of sorted elems | `sorted(t)` |
| Dict | sorted KEYS as list | `sorted(d)` or `sorted(d.keys())` |
| Dict | sorted by VALUE | `sorted(d.items(), key=lambda x: x[1])` |
| Set | sorted as list | `sorted(s)` |
| Any iterable | custom key sort | `sorted(it, key=...)` |

## Why only lists have `.sort()`

Lists are **mutable** → can be sorted in-place.

Strings, tuples, frozensets are **immutable** → can't be modified, need a new object. So they don't have `.sort()`. Only `sorted()` works on them, and it returns a new list.

```python
[1, 2].sort()        # ✓ list method, in-place
"hi".sort()          # ❌ AttributeError — strings are immutable
(1, 2).sort()        # ❌ AttributeError — tuples are immutable
{1, 2}.sort()        # ❌ AttributeError — sets aren't ordered
{1: 'a'}.sort()      # ❌ AttributeError — dicts have order but no sort method
```

For all those non-list types: use `sorted()` (the free function).

## Common gotchas

### Gotcha 1 — `sorted(string)` returns a LIST, not a string

```python
sorted("banana")           # → ['a', 'a', 'a', 'b', 'n', 'n']  ← LIST
''.join(sorted("banana"))  # → "aaabnn"  ← string
```

To use as a dict key (e.g., LC 49 anagram grouping), MUST join back to string. Lists are unhashable.

### Gotcha 2 — `arr.sort()` returns `None`

```python
arr = [3, 1, 2]
new = arr.sort()            # new is None
arr = arr.sort()            # arr is now None — DESTROYED original!
```

In-place ops always return None in Python. Don't assign. Just call:
```python
arr.sort()                  # mutates arr
print(arr)                  # [1, 2, 3]
```

### Gotcha 3 — sorting a dict sorts by keys, not values

```python
d = {'z': 1, 'a': 5, 'm': 3}
sorted(d)                   # → ['a', 'm', 'z']  (KEYS only)
```

To sort by VALUE:
```python
sorted(d.items(), key=lambda x: x[1])
# → [('z', 1), ('m', 3), ('a', 5)]
```

### Gotcha 4 — `sorted` doesn't mutate

```python
arr = [3, 1, 2]
sorted(arr)                 # returns [1, 2, 3]
print(arr)                  # [3, 1, 2] — UNCHANGED
```

`sorted` ALWAYS creates a new object. Doesn't touch the input.

### Gotcha 5 — case-sensitive sort by default

```python
sorted(["banana", "Apple", "cherry"])
# → ['Apple', 'banana', 'cherry']   (uppercase before lowercase due to ASCII)
```

Case-insensitive:
```python
sorted(words, key=str.lower)
```

### Gotcha 6 — can't negate strings for descending

```python
sorted([("a", 5), ("b", 3)], key=lambda x: -x[1])    # ✓ negate int — desc on int
sorted(["banana", "apple"], key=lambda x: -x)         # ❌ TypeError — can't negate string
sorted(["banana", "apple"], reverse=True)             # ✓ use reverse=True for strings
```

## Performance

| Operation | Time | Space |
|---|---|---|
| `sorted(arr)` | O(n log n) | O(n) — new list allocated |
| `arr.sort()` | O(n log n) | O(1) extra — in-place |

Both use Timsort (Python's hybrid stable sort). Same speed; `sort()` saves the new-list allocation.

## When to choose which

| Situation | Pick |
|---|---|
| Need original list untouched | `sorted(arr)` |
| Don't need original, optimize space | `arr.sort()` |
| Sorting a string for hashable key (LC 49) | `''.join(sorted(s))` |
| Sorting tuples by index | `sorted(items, key=lambda x: x[i])` |
| Sorting dict by value | `sorted(d.items(), key=lambda x: x[1])` |

## TL;DR (one line)

> **`sorted()` is the universal free function (any iterable → new list). `list.sort()` is the in-place method (lists only, returns None). For strings, use `''.join(sorted(s))` to get a sorted STRING.**
