# cmp_to_key — when tuple keys aren't enough

**TL;DR:** `functools.cmp_to_key` converts `(a, b) -> -1/0/+1` into a sort key. Use when comparison is **pairwise relative** and can't be expressed as a function of one element alone.

**Patterns:** custom_sort, pairwise_comparison

## The comparator contract

```python
def compare(a, b):
    if a should come before b: return -1
    if a should come after  b: return  1
    return 0
```

## Where it matters — with usage

### LC 179 Largest Number — the canonical example
```python
from functools import cmp_to_key

def largestNumber(nums):
    # we want the arrangement that gives the biggest string
    # for any two strings a, b — put `a` first if a+b > b+a lexicographically
    def compare(a, b):
        if a + b > b + a: return -1       # a before b
        if a + b < b + a: return  1       # a after b
        return 0

    strs = [str(n) for n in nums]
    strs.sort(key=cmp_to_key(compare))
    result = "".join(strs)
    return "0" if result[0] == "0" else result     # [0,0,0] → "0", not "000"
```

**Why tuple key fails:** "a before b" depends on both `a+b` and `b+a` — there's no function `f(a)` alone that gives the right sort order.

### LC 937 Reorder Log Files — sometimes cmp_to_key, often tuple key suffices
```python
from functools import cmp_to_key
# tuple key version (simpler):
def reorderLogFiles(logs):
    def key(log):
        _id, rest = log.split(" ", 1)
        is_digit = rest[0].isdigit()
        return (is_digit, rest if not is_digit else "", _id if not is_digit else "")
    return sorted(logs, key=key)

# cmp_to_key version (for when the rules are truly pairwise):
def compare(log1, log2):
    ...
```

### Custom pairwise ordering problems
- LC 2418 Sort the People — tuple key works, don't overcomplicate
- LC 524 Longest Word Dictionary — `sorted(words, key=lambda w: (-len(w), w))` — tuple key
- When a tuple key works, USE IT. `cmp_to_key` is slower.

## When tuple key DOES work — prefer it

```python
sorted(people, key=lambda p: (p.age, -p.salary, p.name))
# three independent priorities, each a function of p alone
```

## Complexity

- O(n log n) — same as regular sort
- But the **constant is higher**: comparator runs O(n log n) times, while a key function runs just n times (Python's "decorate-sort-undecorate")
- Only use `cmp_to_key` when tuple keys genuinely can't express the order

## Implementing with bigger-is-better semantics

```python
# shorthand for numeric ordering
def compare(a, b): return a - b             # ascending
def compare(a, b): return b - a             # descending
# returns negative/zero/positive — that's all cmp_to_key needs, not exactly -1/0/1
```

**Gotcha 1 — return a SIGN, not a bool.** Returning `True`/`False` gives wrong order (`True == 1` means "a after b" always). Return negative / zero / positive (any sign is fine).

**Gotcha 2 — comparator MUST define a total order.** `cmp(a,b) < 0` and `cmp(b,c) < 0` implies `cmp(a,c) < 0`. Non-transitive comparators produce silently-wrong output, not errors.

**Gotcha 3 — slower than tuple key.** Every problem solvable with tuple keys should use them. `cmp_to_key` is the last resort.
