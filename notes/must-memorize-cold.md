# Must memorize cold — Python idioms for interviews

**TL;DR:** These are the patterns you should type without thinking. In a Google Doc with no autocomplete, every one of these saves you 10-30 seconds. Drill them until reflexive — when they're muscle memory, syntax stops being a tax.

**How to use:** read this file weekly. Cover the right column with your hand and try to recite each. If you can't, you have a gap.

---

## Iteration shapes

| Goal | Code |
|---|---|
| index + value | `for i, val in enumerate(arr):` |
| value only | `for val in arr:` |
| **backward indices** | **`for i in range(len(arr) - 1, -1, -1):`** |
| backward values | `for val in reversed(arr):` |
| parallel iteration | `for a, b in zip(arr1, arr2):` |
| chars of string | `for c in s:` |
| key+value of dict | `for k, v in d.items():` |
| keys only | `for k in d:` |
| values only | `for v in d.values():` |
| every other | `for i in range(0, n, 2):` |
| consecutive pairs | `for a, b in pairwise(arr):` (Py 3.10+) or `for a, b in zip(arr, arr[1:]):` |

## Indexing & slicing

| Goal | Code |
|---|---|
| last element | `arr[-1]` |
| last k elements | `arr[-k:]` |
| first k | `arr[:k]` |
| reverse | `arr[::-1]` |
| every other | `arr[::2]` |
| middle index | `mid = (left + right) // 2` |
| safe slice (no IndexError) | slicing is lenient: `arr[100:200]` returns `[]` |

## Dict

| Goal | Code |
|---|---|
| empty | `d = {}` |
| set | `d[key] = value` |
| read (KeyError if missing) | `d[key]` |
| **read with default** | **`d.get(key, default)`** |
| insert if missing, return value | `d.setdefault(key, []).append(x)` |
| key check | `if key in d:` |
| delete | `del d[key]` |
| iterate pairs | `for k, v in d.items():` |
| frequency dict in 1 line | `Counter(arr)` |
| auto-init missing | `defaultdict(int)`, `defaultdict(list)`, `defaultdict(set)` |

## Set

| Goal | Code |
|---|---|
| empty | `s = set()` *(NOT `{}` — that's a dict!)* |
| literal | `s = {1, 2, 3}` |
| add | `s.add(x)` |
| remove (no error if missing) | `s.discard(x)` |
| remove (KeyError if missing) | `s.remove(x)` |
| membership check | `if x in s:` |
| dedup a list | `set(arr)` |
| intersection | `s1 & s2` |
| union | `s1 \| s2` |
| difference | `s1 - s2` |

## List

| Goal | Code |
|---|---|
| empty | `arr = []` |
| append | `arr.append(x)` |
| pop last | `arr.pop()` (returns the value) |
| pop at index | `arr.pop(i)` *(O(n) — avoid)* |
| insert at index | `arr.insert(i, x)` *(O(n))* |
| sort in place | `arr.sort()` (returns None) |
| sort returning new | `sorted(arr)` |
| sort with key | `sorted(arr, key=lambda x: x[1])` |
| reverse in place | `arr.reverse()` |
| reverse returning new | `arr[::-1]` |
| 2D grid | `[[0]*cols for _ in range(rows)]` *(NOT `[[0]*cols]*rows`!)* |
| flatten | `[x for row in matrix for x in row]` |

## String

| Goal | Code |
|---|---|
| split on whitespace | `s.split()` |
| split on delim | `s.split(",")` |
| join with sep | `"-".join(parts)` |
| join no sep | `"".join(parts)` |
| lowercase | `s.lower()` |
| uppercase | `s.upper()` |
| strip whitespace edges | `s.strip()` |
| replace | `s.replace(old, new)` |
| reverse | `s[::-1]` |
| char checks | `c.isalnum()`, `c.isdigit()`, `c.isalpha()`, `c.isspace()` |
| filter+lowercase | `"".join(c.lower() for c in s if c.isalnum())` |
| count substring | `s.count(sub)` |
| find substring (-1 if missing) | `s.find(sub)` |

## Heap (heapq)

| Goal | Code |
|---|---|
| import | `import heapq` |
| empty heap | `h = []` |
| push | `heapq.heappush(h, x)` |
| pop smallest | `heapq.heappop(h)` |
| peek smallest | `h[0]` |
| heapify list in place | `heapq.heapify(arr)` |
| top k largest | `heapq.nlargest(k, arr)` |
| top k smallest | `heapq.nsmallest(k, arr)` |
| max-heap (workaround) | push `-x`, pop and negate back |
| top k pattern | `heappush` then if `len > k`: `heappop` |

## Deque (BFS queue)

| Goal | Code |
|---|---|
| import | `from collections import deque` |
| empty | `q = deque()` |
| init with values | `q = deque([1,2,3])` |
| add right | `q.append(x)` |
| add left | `q.appendleft(x)` |
| remove right | `q.pop()` |
| remove left | `q.popleft()` |

## Counter

| Goal | Code |
|---|---|
| import | `from collections import Counter` |
| build | `c = Counter(arr)` |
| top k | `c.most_common(k)` |
| anagram check | `Counter(s) == Counter(t)` |
| missing key returns 0 | `c[unseen] == 0` |

## Math

| Goal | Code |
|---|---|
| absolute value | `abs(x)` |
| floor division | `n // 10` |
| modulo | `n % 10` |
| both at once | `q, r = divmod(n, 10)` |
| min/max | `min(a, b)`, `max(arr)`, `min(arr, key=lambda x: x[1])` |
| modular pow | `pow(base, exp, mod)` *(fast)* |
| infinity | `float('inf')` or `float('-inf')` |
| INT_MAX (32-bit) | `2**31 - 1` |
| GCD | `from math import gcd; gcd(a, b)` |
| GCD of list | `from functools import reduce; reduce(gcd, arr)` |
| char to index 0..25 | `ord(c) - ord('a')` |
| index to char | `chr(i + ord('a'))` |

## None / null / empty

| Goal | Code |
|---|---|
| "null" in Python | `None` (NOT `null`) |
| check None | `if x is None:` (use `is`, not `==`) |
| check non-None | `if x is not None:` |
| empty check (any falsy) | `if not x:` (matches None, 0, "", [], {}) |
| default-None pattern | `def f(arr=None): arr = arr or []` |

## Class basics

| Goal | Code |
|---|---|
| class declaration | `class MyClass:` |
| init signature | `def __init__(self, k, nums):` |
| store attribute | `self.k = k` |
| read attribute (inside) | `self.k` |
| call own method | `self._helper()` |
| inheritance | `class Child(Parent):` |
| call super init | `super().__init__()` |

## Linked list

| Goal | Code |
|---|---|
| read next | `curr.next` |
| **walk to next** | **`curr = curr.next`** |
| empty check | `if not head:` or `if head is None:` |
| dummy node pattern | `dummy = ListNode(0); dummy.next = head` |
| three-way swap (reverse step) | `curr.next, prev, curr = prev, curr, curr.next` |
| traverse to end | `while curr.next: curr = curr.next` |

## Tree (binary tree)

| Goal | Code |
|---|---|
| DFS recursion shape | `def dfs(node): if not node: return ...; dfs(node.left); dfs(node.right)` |
| BFS shape | `q = deque([root]); while q: node = q.popleft(); q.append(node.left if node.left else ...)` |
| empty check | `if not root:` or `if root is None:` |

## Comprehension shapes

| Goal | Code |
|---|---|
| filter | `[x for x in arr if x > 0]` |
| transform | `[x*2 for x in arr]` |
| filter + transform | `[x*2 for x in arr if x > 0]` |
| 2D init | `[[0]*cols for _ in range(rows)]` |
| flatten | `[x for row in m for x in row]` |
| dict comp | `{x: i for i, x in enumerate(arr)}` |
| set comp | `{x*2 for x in arr}` |
| generator (lazy) | `(x*2 for x in arr)` |
| count with condition | `sum(1 for x in arr if cond(x))` |
| filter + transform + join (string) | `"".join(c.lower() for c in s if c.isalnum())` |

## Boolean reductions

| Goal | Code |
|---|---|
| at least one matches | `any(x > 0 for x in arr)` |
| all match | `all(x > 0 for x in arr)` |
| count matches | `sum(1 for x in arr if cond(x))` |
| sum (True == 1) | `sum(c == 'a' for c in s)` |

## Common imports (memorize)

```python
from collections import Counter, defaultdict, deque, OrderedDict
from functools import cache, reduce
from itertools import combinations, permutations, accumulate, pairwise
from math import gcd, lcm, inf, sqrt
from typing import List, Optional
import heapq, bisect, math
```

---

## The drill protocol

1. **Cover the right column with your hand.** Read the goal, type the code from memory.
2. **If you hesitate ≥3 seconds**, that's a gap. Note it.
3. **Re-drill the gaps weekly.** Patterns that take >3 seconds to recall WILL slow you in interview.
4. **Goal:** every entry above types under 1 second, no thinking.

This file is the syntax target. Patterns NOT in this file are not muscle-memory candidates — only what's here matters.

## What to add as you grow

When you encounter a Python idiom THREE times in different problems, add it here. Three uses = it's not a one-off, it's a pattern. Add and drill.
