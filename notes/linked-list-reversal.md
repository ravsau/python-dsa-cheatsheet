# Linked list reversal — iterative vs recursive

**TL;DR:** Two ways to reverse a singly linked list. Iterative uses 3 pointers + a loop (O(1) extra space). Recursive walks to the end, then flips arrows on the way back up the call stack (O(n) stack space, more elegant but heavier).

**Patterns:** linked_list, pointer_rewiring, recursion

## Where it matters

- LC 206 Reverse Linked List — the canonical
- LC 92 Reverse Linked List II — reverse a sub-range
- LC 25 Reverse Nodes in k-Group — repeated reversals
- LC 234 Palindrome Linked List — reverse second half + compare
- LC 143 Reorder List — reverse second half + interleave

## Iterative — 3 pointers, one pass (O(1) space) ⭐

The interview default. Walk forward, flip arrows as you go.

### 4-line expanded form (clearest, write this first)
```python
def reverseList(head):
    prev = None
    curr = head
    while curr:
        next_node = curr.next     # 1. SAVE: remember what's ahead
        curr.next = prev          # 2. FLIP: point curr backward
        prev = curr               # 3. ADVANCE prev
        curr = next_node          # 4. ADVANCE curr
    return prev                    # prev is the new head
```

### 1-line tuple-swap form (advanced — same logic, compressed)
```python
while curr:
    curr.next, prev, curr = prev, curr, curr.next
```

Python evaluates the entire RHS as a snapshot tuple **before** any LHS assignment, so the simultaneous swap works without a temp variable.

**Visual trace on `1 → 2 → 3 → None`:**
```
Initial:  prev=None, curr=1
  1 → 2 → 3 → None

After iter 1 (flip 1→None):
  None ← 1     2 → 3 → None
              prev=1, curr=2

After iter 2 (flip 2→1):
  None ← 1 ← 2     3 → None
              prev=2, curr=3

After iter 3 (flip 3→2):
  None ← 1 ← 2 ← 3
              prev=3, curr=None  ← loop exits

Return prev=3 (new head)
```

## Recursive — walk to end, flip on the way back (O(n) stack space)

```python
def reverseList(head):
    if not head or not head.next:           # base case: empty or single node
        return head

    new_head = reverseList(head.next)        # dive ALL the way to the end first

    head.next.next = head                    # ← THE FLIP (see mental model below)
    head.next = None                         # cut head's old forward pointer
    return new_head                          # bubble new head up unchanged
```

### The mental model for `head.next.next = head` 🔑

> **"The node in front of me, point back at me."**

Read the chain left-to-right:
- `head` → me
- `head.next` → the node in front of me
- `head.next.next` → that node's forward pointer
- `= head` → set it to me

You're reaching one node ahead, grabbing its `.next`, and pointing it back to you.

### Why "dive does nothing, ascent does everything"

Look at the structure:
```python
new_head = reverseList(head.next)   # ← PAUSE here on dive
head.next.next = head                # ← RESUME here on ascent
head.next = None
return new_head
```

Everything **before** the recursive call runs on the way DOWN. Everything **after** runs on the way UP. The base-case check is the only thing on the descent path → no flips happen until we hit the bottom and start unwinding.

### Trace on `1 → 2 → 3`

**Dive (nothing flipped, just stacking frames):**
```
reverse(1) called → head=1, recurse on 2
  reverse(2) called → head=2, recurse on 3
    reverse(3) called → head=3, head.next=None → BASE CASE, return 3
```

**Unwind (flip on each return):**
```
← back in reverse(2): new_head = 3
  head.next.next = head  →  3.next = 2     (flip!)
  head.next = None        →  2.next = None  (cut)
  return 3                                   (bubble up)

← back in reverse(1): new_head = 3 (passed up unchanged)
  head.next.next = head  →  2.next = 1     (flip!)
  head.next = None        →  1.next = None  (cut)
  return 3                                   (final new head)
```

Result: `3 → 2 → 1 → None` ✓

### Why `new_head` never changes during unwind

It's set ONCE at the base case (returns the original tail) and is just passed up unchanged through every return. It's a baton handed back to the caller — the new head of the reversed list.

### The "free prev" insight

In iterative, you carry `prev` explicitly. In recursive, **the call stack IS your chain of prev pointers** — when frame `reverse(2)` resumes after returning from `reverse(3)`, the local `head=2` is still in scope, automatically. That's the elegance — at the cost of O(n) stack frames.

## Iterative vs recursive — which to use

| Factor | Iterative | Recursive |
|---|---|---|
| Space | **O(1)** | O(n) — recursion stack |
| Speed | Faster (no call overhead) | Slower (function call per node) |
| Production | ✅ what you ship | ❌ blows stack on huge lists |
| Interview default | ✅ first answer | mention as follow-up |
| Elegance | mechanical | elegant once it clicks |
| Stack overflow risk | none | breaks on n > ~1000 in Python |

**Rule:** ship iterative. Know recursive for when the interviewer asks "can you do it the other way?"

## Gotchas

**Gotcha 1 — recursive needs `head.next = None` after the flip.** Without it, the original forward pointer remains, creating a cycle (`1 → 2 → 1 → 2 → ...`). This line gets fixed in the parent frame, but the LAST node (now at the head of the reversed list) needs its `.next` set to None — and that's exactly what this line does for every node, including the final one.

**Gotcha 2 — base case must check `not head OR not head.next`.** The `not head` handles empty input. The `not head.next` handles single-node input AND ensures we don't try to recurse on `head.next.next` of None. Both conditions return `head` as-is.

**Gotcha 3 — Python's recursion limit.** Default is ~1000. For a 5000-node list (LC 206 max constraint), you may hit `RecursionError`. Either bump `sys.setrecursionlimit(10**4)` or just use iterative.

**Gotcha 4 — the iterative tuple-swap order.** `curr.next, prev, curr = prev, curr, curr.next` — the RHS order is `(prev, curr, curr.next)`, NOT `(prev, curr.next, curr)`. Get this wrong and the list shifts wrong. Stick to the 4-line expanded form unless you've drilled the one-liner.
