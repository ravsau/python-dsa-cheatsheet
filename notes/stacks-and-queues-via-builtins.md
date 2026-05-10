# Stacks and queues via Python built-ins

**TL;DR:** Python doesn't have a separate `Stack` or `Queue` class. Use a regular `list` for stacks (LIFO). Use `collections.deque` for queues (FIFO). Don't use `list` for queues — `pop(0)` is O(n).

**Where it matters:**
- LC 20 Valid Parentheses — stack via list
- LC 155 Min Stack — two parallel lists
- LC 102 Binary Tree Level Order — queue via deque
- LC 200, 994 — BFS via deque
- LC 232 Implement Queue using Stacks — combining both

**Patterns:** stack, queue, deque, builtin_choice

## Stack — use a Python list

A list IS a stack. All stack ops are O(1):

```python
stack = []                  # empty stack

stack.append(x)             # PUSH (add to top) — O(1)
stack.pop()                 # POP (remove top, return) — O(1)
stack[-1]                   # PEEK top (no remove) — O(1)
not stack                   # IS EMPTY — O(1) (truthy check)
len(stack)                  # SIZE — O(1)
```

Why list works as a stack: appending to the end is O(1), popping from the end is O(1). The "top" is the END of the list, accessible via `[-1]`.

```python
# Example — basic stack usage (LC 20 Valid Parens)
stack = []
for char in s:
    if char in '([{':
        stack.append(char)
    else:
        if not stack or stack.pop() != opening_pair[char]:
            return False
return not stack
```

Don't import anything for stacks — `list` is built-in.

## Queue — use `collections.deque`

A `deque` (double-ended queue) is the right tool for FIFO operations. **Don't use a regular list** — `pop(0)` is O(n) because it shifts all elements left.

```python
from collections import deque

q = deque()                 # empty queue
q = deque([1, 2, 3])         # initialize with values
q = deque([root])            # one item — wrap in list

q.append(x)                  # ENQUEUE (add to back) — O(1)
q.popleft()                  # DEQUEUE (remove front, return) — O(1)
q[0]                         # PEEK front (no remove) — O(1)
not q                        # IS EMPTY — O(1)
len(q)                       # SIZE — O(1)
```

Less common but supported (deque is double-ended):
```python
q.appendleft(x)              # add to FRONT — O(1)
q.pop()                      # remove from BACK — O(1)
```

## Why NOT use list as queue

```python
q = [1, 2, 3]
q.pop(0)                     # ❌ O(n) — shifts every element left
```

For 1000 dequeues on a list-queue: 1000 × n shifts = O(n²) total. Bad for BFS on large grids.

```python
q = deque([1, 2, 3])
q.popleft()                  # ✓ O(1)
```

## Stack vs Queue summary

| | Stack (LIFO) | Queue (FIFO) |
|---|---|---|
| Data structure | Python `list` | `collections.deque` |
| Add | `append(x)` to end | `append(x)` to back |
| Remove | `pop()` from end | `popleft()` from front |
| Peek | `[-1]` last element | `[0]` first element |
| Import? | No (list is built-in) | Yes (`from collections import deque`) |
| Time per op | O(1) | O(1) |

Both have O(1) for the operations they support. Use the right tool for the access pattern.

## When to use which

| Situation | Pick |
|---|---|
| LIFO order (last in, first out) | Stack — Python list |
| FIFO order (first in, first out) | Queue — deque |
| BFS traversal | deque (FIFO) |
| DFS iterative | list (LIFO) — explicit stack |
| Tree DFS recursive | recursion stack (implicit list) |
| Both ends needed (rare) | deque |
| Min-stack / max-stack | Two parallel lists (LC 155) |

## Common patterns

### Recursive function call stack — IMPLICIT stack
When you write recursive DFS, you're using Python's CALL STACK as a stack:

```python
def dfs(node):
    if not node: return
    do_something(node)
    dfs(node.left)         # pushes a new frame onto the call stack
    dfs(node.right)        # pushes another frame
                           # frames pop as functions return
```

You don't need an explicit list — the call stack does it.

### Iterative DFS — EXPLICIT stack via list

```python
stack = [root]
while stack:
    node = stack.pop()      # LIFO — last pushed processed first
    if node.right: stack.append(node.right)
    if node.left:  stack.append(node.left)
```

### BFS — explicit queue via deque

```python
from collections import deque
q = deque([root])
while q:
    node = q.popleft()       # FIFO — first pushed processed first
    if node.left:  q.append(node.left)
    if node.right: q.append(node.right)
```

## Min stack pattern (LC 155)

Sometimes you need O(1) access to the MIN (or MAX) of a stack. Solution: maintain a parallel stack of running mins.

```python
class MinStack:
    def __init__(self):
        self.stack = []
        self.min_stack = []
    
    def push(self, val):
        self.stack.append(val)
        if not self.min_stack or val <= self.min_stack[-1]:
            self.min_stack.append(val)
        else:
            self.min_stack.append(self.min_stack[-1])    # duplicate the current min
    
    def pop(self):
        self.stack.pop()
        self.min_stack.pop()
    
    def top(self): return self.stack[-1]
    def getMin(self): return self.min_stack[-1]
```

The duplication keeps lengths in sync, so pops stay O(1).

## TL;DR (one line)

> **Stack: use Python list (`list.append`, `list.pop`, `list[-1]`). Queue: use `from collections import deque` (`deque.append`, `deque.popleft`, `deque[0]`). Don't mix — list is bad for queue, deque is overkill for stack.**
