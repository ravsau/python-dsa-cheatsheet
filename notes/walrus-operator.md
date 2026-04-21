# Walrus `:=` — assign + use in one expression

**TL;DR:** `:=` (Python 3.8+) binds a value inside an expression. Eliminates the "compute twice" pattern in while loops and conditionals.

**Patterns:** linked_list_traversal, parse_loops

## Where it matters — with usage

### LC 234 Palindrome Linked List — walk + compare without separate `next`
```python
def isPalindrome(head):
    vals = []
    while (node := head):                     # walrus binds head → node
        vals.append(node.val)
        head = head.next
    return vals == vals[::-1]
```

### LC 876 Middle of Linked List — fast/slow advance
```python
slow = fast = head
while fast and (fast := fast.next):           # bind AND test in same expression
    slow = slow.next
    fast = fast.next                          # advance fast a second time
return slow
```

### LC 20 Valid Parentheses — pop + check in one line
```python
def isValid(s):
    pairs = {')': '(', ']': '[', '}': '{'}
    stack = []
    for c in s:
        if c in pairs:
            if not stack or (top := stack.pop()) != pairs[c]:
                return False
            # `top` is still in scope here if we want to log it
        else:
            stack.append(c)
    return not stack
```

### Parsing loop — regex iterator idiom
```python
import re
pos = 0
while (m := re.search(r"\d+", s, pos)):       # find and bind at once
    print(m.group(), m.start())
    pos = m.end()
```

### List comp with compute-filter-reuse
```python
# keep x only if compute(x) is truthy, AND use that computed value
results = [y for x in data if (y := expensive_compute(x))]
```

## Common shape — replacing compute-twice

```python
# before
chunk = read()
while chunk:
    process(chunk)
    chunk = read()              # duplicated

# after
while (chunk := read()):
    process(chunk)
```

**Gotcha 1 — parens are often required.** `while x := read():` is a SyntaxError. Use `while (x := read()):`.

**Gotcha 2 — don't overuse.** If three things happen inside the walrus, break it out. The reader should be able to say it aloud naturally.

**Gotcha 3 — scope leaks to enclosing block.** The name `y` above is accessible after the comprehension ends — by design, occasionally surprising.
