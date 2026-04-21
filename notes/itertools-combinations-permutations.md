# itertools.combinations / permutations / product

**TL;DR:** When the problem literally says "all combinations / permutations / Cartesian product," stdlib gives it to you. Hand-roll backtracking only when you need pruning or early termination.

**Patterns:** enumeration, backtracking_alternative

```python
from itertools import combinations, permutations, product, combinations_with_replacement

combinations([1,2,3], 2)                    # (1,2) (1,3) (2,3)          C(n,k)
permutations([1,2,3], 2)                    # (1,2) (1,3) (2,1) (2,3) ... P(n,k)
permutations([1,2,3])                       # all orderings               n!
combinations_with_replacement([1,2,3], 2)   # (1,1) (1,2) (1,3) (2,2) ...
product([1,2], [3,4])                       # (1,3) (1,4) (2,3) (2,4)   Cartesian
product([0,1], repeat=3)                    # all 3-bit strings
```

## Where it matters — with usage

### LC 77 Combinations — literally one line
```python
def combine(n, k):
    return [list(c) for c in combinations(range(1, n+1), k)]
```

### LC 78 Subsets — combinations for each size
```python
def subsets(nums):
    return [list(c) for r in range(len(nums)+1) for c in combinations(nums, r)]
    # r=0 → [()]
    # r=1 → [(1,),(2,),(3,)]
    # r=2 → [(1,2),(1,3),(2,3)]
    # r=3 → [(1,2,3)]
```

### LC 46 Permutations — all orderings of distinct values
```python
def permute(nums):
    return [list(p) for p in permutations(nums)]
```

### LC 47 Permutations II — dedupe via set
```python
def permuteUnique(nums):
    return [list(p) for p in set(permutations(nums))]
    # works but loses the pruning that manual backtracking offers
    # n! permutations generated before dedup → suboptimal for large n
```

### LC 17 Letter Combinations of Phone Number — Cartesian product
```python
def letterCombinations(digits):
    if not digits: return []
    mapping = {'2':'abc','3':'def','4':'ghi','5':'jkl',
               '6':'mno','7':'pqrs','8':'tuv','9':'wxyz'}
    letters = [mapping[d] for d in digits]       # ['abc', 'def'] for "23"
    return ["".join(p) for p in product(*letters)]
    # product(*) unpacks: product('abc', 'def') → all combos
```

### LC 1001 — combinations_with_replacement use case
```python
# "choose k items from n, allow repeats"
combinations_with_replacement(range(10), 3)
# (0,0,0) (0,0,1) ... (9,9,9)
```

### LC 39 Combination Sum — DON'T use stdlib; needs pruning
```python
# itertools has no target-sum pruning — you'd enumerate all subsets then filter
# Hand-rolled backtracking with early-exit-when-sum>target is correct here
```

## When to hand-roll backtracking instead

| Situation | Reason |
|---|---|
| Target sum / constraint pruning | stdlib can't prune during enumeration |
| Want first solution, not all | stdlib yields everything; you want early exit |
| Duplicates with skip rules (LC 40) | easier with sorted + skip than `set()` dedup |
| Interviewer says "don't use stdlib" | signal-check — know the hand-roll too |

## Complexity

- `combinations(n, k)` — yields C(n, k) tuples, each O(k) to materialize
- `permutations(n, k)` — yields P(n, k) = n! / (n-k)!
- `product(iter1, iter2, ..., repeat=r)` — yields product of sizes

All lazy generators — O(1) to create, O(1) extra space until you materialize.

**Gotcha 1 — output is tuples.** Wrap with `list(...)` or `"".join(...)` per use case.

**Gotcha 2 — permutations with duplicate values repeats outputs.** `permutations([1,1,2])` yields 6, not 3 unique. Wrap in `set(...)` OR skip stdlib and hand-roll.

**Gotcha 3 — iterator is one-shot.** After one pass, exhausted. Wrap in `list(...)` if you'll traverse twice.
