# In-place vs returns-new — Python's silent footgun

**TL;DR:** Many Python operations come in TWO flavors — `in-place` (modifies the original, returns `None`) and `returns-new` (creates a new object). Assigning the return of an in-place operation gives you `None`, not the result. This is the #1 source of "why is my variable None?" bugs.

**Where it matters:**
- Sort: `list.sort()` (in-place, None) vs `sorted(list)` (new list)
- Reverse: `list.reverse()` (in-place, None) vs `reversed(list)` / `list[::-1]` (new)
- Heapify: `heapq.heapify(L)` (in-place, None) — the canonical trap
- Append: `list.append(x)` (in-place, None)
- Update: `dict.update(d)` (in-place, None)

**Patterns:** mutation_vs_value, side_effect, return_type

## The principle

| Flavor | Effect | Return value | Convention |
|---|---|---|---|
| **In-place (mutating)** | Modifies the original | `None` (or popped item for `pop`) | Often instance methods (`arr.sort()`) or stdlib functions on lists (`heapq.heapify`) |
| **Returns-new** | Creates a new object, leaves original | The new object | Often built-in functions (`sorted(arr)`, `reversed(arr)`) |

**The trap:** assigning the return of an in-place op:
```python
arr = arr.sort()          # ❌ arr is now None
arr = heapq.heapify(arr)  # ❌ arr is now None
```

## Complete heapq table

```python
import heapq

# IN-PLACE (mutating)
heapq.heapify(L)              # → None         (turns L into a heap in place)
heapq.heappush(L, x)          # → None         (adds x to heap L)
heapq.heappop(L)              # → popped item  (removes & returns smallest from L)
heapq.heappushpop(L, x)       # → popped item  (push then pop, atomic)
heapq.heapreplace(L, x)       # → popped item  (pop then push, atomic)

# RETURNS-NEW
heapq.nlargest(k, L)          # → new list of k largest
heapq.nsmallest(k, L)         # → new list of k smallest
heapq.merge(*lists)           # → iterator merging sorted iterables
```

The first 5 mutate. Of those, 3 return useful values (the popped item). Two return None.

**For LeetCode top-K**, you only need `heapify`, `heappush`, `heappop`. The first two return None, the third returns the popped item.

## Complete list (mutators) table

```python
arr = [3, 1, 2]

# IN-PLACE — return None
arr.sort()                    # sort in place
arr.sort(key=lambda x: -x)    # sort by key
arr.reverse()
arr.append(4)
arr.extend([5, 6])
arr.insert(0, 0)
arr.remove(3)                  # remove first occurrence
arr.clear()

# IN-PLACE — return the popped item
arr.pop()                      # last item
arr.pop(0)                     # item at index

# RETURNS-NEW
sorted(arr)                    # new sorted list
reversed(arr)                  # iterator (call list() to materialize)
arr[::-1]                      # new reversed list (slice)
arr.copy()                     # shallow copy
list(arr)                      # shallow copy
arr + [7]                      # new list (concatenation)
[x*2 for x in arr]             # new list (comprehension)
```

## Why heapq is the canonical trap

Most Python list mutators are **methods on the list** (e.g., `arr.sort()`). The dot-syntax visually signals "mutating." But `heapq` is a **module** with functions, so:

```python
heapq.heapify(L)         # looks like a function call that returns something
```

You instinctively expect `heap = heapq.heapify(L)` to give you the heap. It doesn't — it gives you `None`. The list `L` itself is now the heap.

This is a Python idiosyncrasy. Just remember: heapq mutates in place.

## The mental rule

> **"Verb done TO the thing"** → in-place, returns None.
> **"Version OF the thing"** → returns new.

```python
arr.sort()        # "sort the array" — verb done to arr        → in-place
sorted(arr)       # "a sorted version of arr"                   → new

arr.reverse()     # "reverse the array"                          → in-place
reversed(arr)     # "reversed view of arr"                       → new (iterator)
arr[::-1]         # "reverse-sliced copy"                        → new

heapq.heapify(L)  # "heapify the list" — verb on L               → in-place
heapq.nlargest    # "largest values from L (no mutation)"        → new
```

If the function name reads as a verb done TO an object, expect in-place. If it reads as a version/view OF an object, expect returns-new.

## Common bugs

```python
# Bug 1 — sort
nums = [3, 1, 2]
nums = nums.sort()              # ❌ nums is now None
print(nums[0])                   # 💥 TypeError: 'NoneType' is not subscriptable

# Fix:
nums.sort()                      # don't reassign

# Bug 2 — heapify
heap = heapq.heapify([3, 1, 2])  # ❌ heap is None
heapq.heappop(heap)              # 💥 TypeError

# Fix:
nums = [3, 1, 2]
heapq.heapify(nums)              # nums is now a heap
heapq.heappop(nums)              # works

# Bug 3 — append
arr = [1, 2, 3]
arr = arr.append(4)              # ❌ arr is now None (the 4 went into the original list, but you reassigned)
print(arr)                        # None

# Fix:
arr.append(4)                    # call without assignment
print(arr)                        # [1, 2, 3, 4]
```

## How to debug "why is this variable None?"

When a variable is unexpectedly `None`, check if you assigned the return of an in-place operation. Print or read the docs to see what the function returns.

```python
help(heapq.heapify)              # → "Returns None" in the docs
```

Or just remember: if the operation conceptually MUTATES the input (sort, reverse, heapify, append), it usually returns None.

## Why Python does this

It's an explicit design choice: **operations that mutate return None to discourage chaining.**

```python
# This DOESN'T work in Python:
result = arr.sort().append(x).reverse()    # all None — chain breaks immediately

# Compared to JavaScript (which DOES allow chaining):
[1,2,3].sort().reverse().filter(x => x > 1)
```

Python's stance: if you're mutating, do it explicitly, line by line. Don't chain. The None return enforces this.

(`pop()` is the exception because you usually want the popped item.)

## TL;DR (one line)

> **In-place ops (sort, reverse, append, heapify, heappush) mutate the original and return `None`. Returns-new ops (sorted, reversed, list copy, slice) create new objects. Don't assign the return of an in-place op — you'll get `None`. The heapq module is the canonical trap because the function-call syntax LOOKS like it should return a heap.**
