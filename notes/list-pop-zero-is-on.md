# list.pop(0) is O(n) — use deque

**TL;DR:** `list.pop(0)` shifts every remaining element left → O(n). A BFS using `list.pop(0)` is O(n²) total. Use `collections.deque.popleft()` — O(1).

**Where it matters:**
- LC 102 Binary Tree Level Order — BFS
- LC 127 Word Ladder — BFS
- LC 200 Number of Islands — BFS flood fill
- LC 207 Course Schedule — Kahn's (topo BFS)
- LC 994 Rotting Oranges — multi-source BFS
- *any* BFS-style problem

**Patterns:** bfs, topological_sort (Kahn's), multi-source-bfs

**Complexity impact:** This single mistake turns O(V+E) BFS into O(V·(V+E)) ≈ O(V²). On LC 127 (word ladder with 5000-word dict) this is TLE-level.

**Code:**
```python
from collections import deque

q = deque([start])
while q:
    node = q.popleft()           # O(1)
    for nb in neighbors(node):
        q.append(nb)             # O(1)
```

**`list` method cost reference:**

| op | cost |
|---|---|
| `list.append(x)` | O(1) amortized |
| `list.pop()` | O(1) |
| `list.pop(0)` / `list.pop(i)` | **O(n)** |
| `list.insert(0, x)` | **O(n)** |
| `deque.append/appendleft/pop/popleft` | O(1) |

**Gotcha:** Reviewers grade BFS solutions partly on using `deque`. A correct-but-`list.pop(0)` answer signals you don't know the cost model — same point-loss as writing O(n²) when O(n) exists.
