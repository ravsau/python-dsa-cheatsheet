# defaultdict — no KeyError, no init checks

**TL;DR:** `defaultdict(factory)` auto-creates missing keys via the factory. Replaces every `if key not in d: d[key] = []` check.

**Patterns:** graph_adjacency, grouping, frequency

```python
from collections import defaultdict
defaultdict(int)       # default 0 — frequency counts
defaultdict(list)      # default [] — grouping, adjacency
defaultdict(set)       # default set() — unique grouping
defaultdict(lambda: [0]*26)   # 26-slot count array per key
```

## Where it matters — with usage

### LC 133 Clone Graph — adjacency list in 2 lines
```python
graph = defaultdict(list)
for u, v in edges:
    graph[u].append(v)                   # no "if u not in graph: graph[u] = []"
    graph[v].append(u)                   # undirected
```

### LC 207 Course Schedule — topo sort with implicit graph
```python
def canFinish(numCourses, prerequisites):
    graph = defaultdict(list)
    indeg = defaultdict(int)
    for course, pre in prerequisites:
        graph[pre].append(course)        # edge pre → course
        indeg[course] += 1               # defaultdict(int) means indeg[x] starts at 0

    q = deque([c for c in range(numCourses) if indeg[c] == 0])
    visited = 0
    while q:
        c = q.popleft(); visited += 1
        for nxt in graph[c]:
            indeg[nxt] -= 1
            if indeg[nxt] == 0: q.append(nxt)
    return visited == numCourses
```

### LC 49 Group Anagrams — bucket by sorted-string key
```python
def groupAnagrams(strs):
    groups = defaultdict(list)
    for s in strs:
        key = "".join(sorted(s))        # or tuple(count array) for O(n) key
        groups[key].append(s)           # append-without-init
    return list(groups.values())
```

### LC 811 Subdomain Visit Count — bucket counts
```python
def subdomainVisits(cpdomains):
    counts = defaultdict(int)
    for cp in cpdomains:
        n, domain = cp.split()
        n = int(n)
        parts = domain.split(".")
        for i in range(len(parts)):
            counts[".".join(parts[i:])] += 1     # +=int auto-starts from 0
    return [f"{v} {k}" for k, v in counts.items()]
```

**Gotcha 1 — access creates the key.** `d[missing]` **inserts** `missing` with the default, even on a read. Use `key in d` or `d.get(key)` to check without inserting.

**Gotcha 2 — factory must be a callable, not a value.** `defaultdict([])` crashes; `defaultdict(list)` is right.

**Gotcha 3 — can't JSON-serialize directly.** Convert with `dict(d)` at the boundary.
