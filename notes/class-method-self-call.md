# `self.method()` for recursive calls in LeetCode

**TL;DR:** LeetCode wraps every solution in `class Solution:`, so any recursive call to a method on the same class **must use `self.`**. `invertTree(node)` won't work — Python sees it as a free function. You need `self.invertTree(node)`.

**Where it matters:**
- LC 100 Same Tree — `self.isSameTree(p.left, q.left)`
- LC 104 Maximum Depth of Binary Tree — `self.maxDepth(root.left)`
- LC 110 Balanced Binary Tree — `self.isBalanced(...)`
- LC 206 Reverse Linked List (recursive) — `self.reverseList(head.next)`
- LC 226 Invert Binary Tree — `self.invertTree(root.left)`
- LC 543 Diameter of Binary Tree — `self.depth(node.left)`
- Every recursive solution wrapped in `class Solution:`

**Patterns:** recursion, oop, leetcode-template

## The trap

You write the natural recursive shape:

```python
class Solution:
    def invertTree(self, root):
        if not root:
            return None
        root.left, root.right = root.right, root.left
        invertTree(root.left)        # ❌ NameError: name 'invertTree' is not defined
        invertTree(root.right)       # ❌
        return root
```

Python doesn't auto-resolve method names within the class. `invertTree` alone is treated as a free function — and there's no free function with that name → NameError at runtime.

## The fix

```python
class Solution:
    def invertTree(self, root):
        if not root:
            return None
        root.left, root.right = root.right, root.left
        self.invertTree(root.left)        # ✓ explicit dispatch through self
        self.invertTree(root.right)       # ✓
        return root
```

`self` is the instance the method was called on. To call another method on the same instance, you go through `self.`.

## Why other languages don't need it

- **Java / C++:** unqualified `invertTree(root.left)` inside the class is implicit `this.invertTree(...)`. The compiler resolves it.
- **JavaScript:** classes also dispatch through `this.method()`, similar to Python.
- **Python:** explicit-is-better-than-implicit. No magic. You write `self.` every time.

If you came from Java, this is the #1 LeetCode-Python gotcha.

## When you DON'T need self.

`self.` is for methods/attributes **on the instance**. Don't use it for:

- **Local variables:** `result = ...` not `self.result = ...` (unless you intentionally want instance state)
- **Imports:** `heapq.heappush(...)` not `self.heapq.heappush(...)`
- **Built-in functions:** `len(arr)`, `sorted(arr)` — these are free functions
- **Standalone helper functions outside the class:**
  ```python
  def helper(x):           # outside class — free function
      return x * 2

  class Solution:
      def foo(self):
          return helper(5)  # ✓ no self. — helper is not a method
  ```

## The mental rule

> **If the function is `def`'d inside `class Solution:` (or any class), every call to it from another method needs `self.`. Otherwise no `self.`.**

## Sneaky variation — nested helper inside a method

Sometimes you define a helper inside the method (closure-style) to avoid `self.`:

```python
class Solution:
    def maxDepth(self, root):
        def dfs(node):                    # local function inside the method
            if not node:
                return 0
            return 1 + max(dfs(node.left), dfs(node.right))   # ✓ no self. — dfs is local
        return dfs(root)
```

This is **idiomatic Python on LeetCode** and arguably cleaner than `self.` recursion because:
- `dfs` is invisible outside `maxDepth` (encapsulation)
- No `self.` prefix noise
- Closures can capture local state from the enclosing method (e.g., a `nonlocal` accumulator)

Use this style when the recursive helper has a different signature than the public method (e.g., the public method takes `(root)` but the helper needs `(node, accumulator)`).

## Gotcha — `self.method` vs `self.method()`

```python
self.invertTree           # ← reference to the bound method (no call)
self.invertTree(root.left)   # ← actually invokes it
```

Forgetting the `()` is a different bug — you've created a method reference and thrown it away. No error, no recursion, broken result. Always include the parens.

## TL;DR (one line)

> **Inside `class Solution:`, recursive calls to methods on the same instance must be prefixed with `self.` — Python doesn't auto-resolve like Java/C++. The fix is one keyword.**
