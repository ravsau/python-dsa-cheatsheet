# Dict + Doubly-Linked List — the O(1) ordering pattern

**TL;DR:** When you need O(1) lookup AND O(1) ordering-by-recency, combine a `dict` (key → node) with a doubly-linked list of nodes. The DLL's positional order IS your timestamp — no actual time values needed.

**Patterns:** lru_cache, lfu_cache, ordered_o1, oop_design

**Where it matters:**
- LC 146 LRU Cache — the canonical
- LC 460 LFU Cache (harder — buckets of DLLs)
- LC 432 All O(1) Data Structure
- Real systems: CDN cache, OS page replacement, browser tab history

## The two-data-structure trick

| Need | Pick |
|---|---|
| O(1) lookup "is key in cache?" | `dict` |
| O(1) "mark this as most recent" | DLL (move node to head) |
| O(1) "find the LRU to evict" | DLL (read tail.prev) |
| O(1) "delete by key after eviction" | dict (need the key stored on the node) |

No single data structure does all four. The dict + DLL combo is the only way to hit O(1) for every operation.

## Why DLL beats deque

A `deque` gives O(1) at the ends but O(n) to find/remove a middle element. On every `get`, you need to **move a specific node to the front** — which means removing it from wherever it currently sits. DLL with `prev`/`next` pointers lets you do that in O(1) if you already hold a reference to the node (which the dict gives you).

```
deque:  cannot remove middle element in O(1)  ❌
DLL:    given node ptr, rewire 2 pointers → O(1)  ✓
```

## Sentinels — eliminate edge cases

Two dummy nodes at head and tail that NEVER hold real data:

```
empty cache:  head <-> tail
one item:     head <-> A <-> tail
two items:    head <-> A <-> B <-> tail        (A is MRU)
```

Sentinels guarantee every real node has a non-None `prev` AND non-None `next`. Zero null-checks in insert/remove logic.

```python
self.head = Node()
self.tail = Node()
self.head.next = self.tail
self.tail.prev = self.head
```

## Node must store its own key (the eviction trick)

```python
class Node:
    def __init__(self, key=None, val=None):
        self.key = key          # ← needed for eviction cleanup
        self.val = val
        self.prev = None
        self.next = None
```

Why `key` on the node? When you evict the LRU (`tail.prev`), you need to delete it from the dict. But you only hold the node object — you need its key to do `del self.dic[evicted.key]`. Without `key` on the node, you'd have to scan the dict O(n). The mutual reference is the trick.

## The two helpers — pasteable, memorize cold

```python
def _remove(self, node):
    # unlink node from its current position
    node.prev.next = node.next
    node.next.prev = node.prev

def _add_to_head(self, node):
    # insert right after head sentinel
    first = self.head.next
    node.prev = self.head
    node.next = first
    self.head.next = node
    first.prev = node
```

The order in `_add_to_head` is **wire the new node FIRST, then redirect the neighbors**. If you redirect first you lose your reference.

## The full canonical LRU Cache (LC 146)

```python
class Node:
    def __init__(self, key=None, val=None):
        self.key = key
        self.val = val
        self.prev = None
        self.next = None


class LRUCache:
    def __init__(self, capacity):
        self.dic = {}
        self.capacity = capacity
        self.head = Node()
        self.tail = Node()
        self.head.next = self.tail
        self.tail.prev = self.head

    def _remove(self, node):
        node.prev.next = node.next
        node.next.prev = node.prev

    def _add_to_head(self, node):
        first = self.head.next
        node.prev = self.head
        node.next = first
        self.head.next = node
        first.prev = node

    def get(self, key):
        if key not in self.dic:
            return -1
        node = self.dic[key]
        self._remove(node)          # touch = unlink + reinsert at head
        self._add_to_head(node)
        return node.val

    def put(self, key, value):
        if key in self.dic:                       # case 1: update existing
            node = self.dic[key]
            node.val = value
            self._remove(node)
            self._add_to_head(node)
            return
        if len(self.dic) == self.capacity:        # case 2: evict before inserting
            lru = self.tail.prev
            self._remove(lru)
            del self.dic[lru.key]                  # node.key pays off here
        node = Node(key, value)                    # case 3: insert new
        self.dic[key] = node
        self._add_to_head(node)
```

## Mental model — the one sentence

> **The DLL's positional order IS the recency timestamp. Head side = most recent. Tail side = least recent. Touching a node means: detach + reinsert at head. Eviction = pop tail.prev.**

## Gotchas (every single one costs an interview round)

1. **`self.head = node`** destroys the sentinel. The sentinels NEVER move. Only nodes move. To "move a node to head" means rewire pointers, NEVER reassign `self.head`.

2. **Forgetting `_remove` before `_add_to_head` in `get`.** If you only call `_add_to_head`, the node ends up wired at head AND still wired in its old position → corrupted list.

3. **`self.dic(key)` vs `self.dic[key]`.** Dict access is `[]`, not `()`. Easy to mistype under pressure.

4. **Eviction condition is `==`, not `>`.** Because you evict BEFORE inserting, the dict is exactly AT capacity when you need to evict — never over.

5. **Not storing `key` on Node.** Eviction would force O(n) scan of dict to find which key to delete. Defeats the whole O(1) goal.

6. **Forgetting the early `return` in `put` Case 1.** If you update an existing key and don't return, you fall through into Case 2/3 and re-insert the same key as a new node → duplicated entries.

7. **Sentinel initial wiring backwards.** `self.head.next = self.tail` AND `self.tail.prev = self.head`. Both lines required.

## OrderedDict shortcut — when it's OK

If the interviewer doesn't require explaining the DLL internals, `collections.OrderedDict` gives you an LRU in ~15 lines (it IS dict + DLL under the hood):

```python
from collections import OrderedDict

class LRUCache:
    def __init__(self, capacity):
        self.cap = capacity
        self.cache = OrderedDict()

    def get(self, key):
        if key not in self.cache: return -1
        self.cache.move_to_end(key)
        return self.cache[key]

    def put(self, key, value):
        if key in self.cache:
            self.cache.move_to_end(key)
        self.cache[key] = value
        if len(self.cache) > self.cap:
            self.cache.popitem(last=False)
```

**Use OrderedDict if:** the problem says "implement an LRU cache" and doesn't ask about internals.
**Use dict + DLL if:** the problem says "implement using dict and linked list" or the interviewer asks "how does this work under the hood?" — Google rounds usually want the explicit version.

## TL;DR (one line)

> **dict (key→Node) + doubly-linked list with sentinels. Node stores its own key. Head side = MRU, tail side = LRU. Touch = remove + add_to_head. Evict = pop tail.prev + del dict[node.key]. Order in the list IS the timestamp.**
