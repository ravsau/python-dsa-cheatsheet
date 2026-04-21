# sum() with generator — count-with-condition

**TL;DR:** `sum(1 for x in arr if cond(x))` counts matches in one line. `True == 1`, so `sum(cond(x) for x in arr)` also works.

**Patterns:** frequency, boolean_sum

## Where it matters — with usage

### LC 191 Number of 1 Bits — count set bits
```python
def hammingWeight(n):
    return sum(1 for b in bin(n) if b == '1')
    # equivalent (and faster):  bin(n).count('1')
```

### LC 771 Jewels and Stones — membership count
```python
def numJewelsInStones(jewels, stones):
    J = set(jewels)                                  # O(1) lookup
    return sum(s in J for s in stones)               # bool → int
```

### LC 202 Happy Number — sum of squared digits
```python
def squared_digit_sum(n):
    return sum(int(c)**2 for c in str(n))            # 19 → 1+81 = 82
```

### LC 338 Counting Bits — per-index bit counts
```python
def countBits(n):
    return [bin(i).count('1') for i in range(n+1)]
    # or the sum form:
    # return [sum(1 for b in bin(i) if b=='1') for i in range(n+1)]
```

### LC 1313 Decompress Run-Length — expand and sum
```python
def sumFreq(nums):
    return sum(nums[i] * nums[i+1] for i in range(0, len(nums), 2))
    # (freq, value) pairs → total count × value contribution
```

### LC 2011 Final Value After Operations — net counter
```python
def finalValueAfterOperations(ops):
    return sum(1 if '+' in op else -1 for op in ops)      # +1 or -1 each
```

## Memory profile — generator vs list

```python
sum(x for x in arr)            # O(1) extra memory (lazy)
sum([x for x in arr])          # O(n) — builds full list first
```

Always use the generator form inside `sum()`. Same result, lower memory.

**Gotcha 1 — `sum` on strings is banned.** `sum(["a", "b"])` → TypeError. Use `"".join(...)`.

**Gotcha 2 — `True == 1` but `True is not 1`.** `sum(x == 3 for x in arr)` counts 3s correctly. Don't write `if flag == True:` in other code — just `if flag:`.

**Gotcha 3 — empty iterable returns 0.** `sum([])` → 0. Useful or a silent-zero bug depending on context.
