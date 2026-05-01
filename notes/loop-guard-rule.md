# Loop condition guards — the deepest-dereferencer rule

**TL;DR:** In any pointer-based loop, the loop condition must guarantee every dereference inside the body is safe. The **deepest dereference** dictates the guard chain. Each variable guards itself — never cross-guard.

**Where it matters:**
- LC 141 Linked List Cycle — `while fast and fast.next` (because body does `fast.next.next`)
- LC 21 Merge Two Sorted Lists — `while list1 and list2` (because body does `.val` on both)
- LC 206 Reverse Linked List — `while curr` (because body does `curr.next`)
- LC 19 Remove Nth From End — `while fast` then `while fast.next` (two phases, two guards)
- LC 234 Palindrome LL — same fast/slow guard
- LC 92, 143, 25 — every doubly-walked LL pattern

**Patterns:** loop-condition, two_pointers, fast_slow, null-safety

## The rule

For any expression `a.b.c.d` inside the loop body:

```python
while a and a.b and a.b.c:    # everything UP TO (not including) the last .field
    ... a.b.c.d ...           # the deepest read — fully safe now
```

You guard **every parent** of the deepest dereference. The final field is the value you want, not a node to guard.

For Floyd's cycle detection — body does `fast.next.next`:
- Pieces: `fast` → `fast.next` → `fast.next.next`
- The thing you want is `fast.next.next`
- Parents needing guards: `fast`, `fast.next`
- Guard: `while fast and fast.next:`

## Each variable guards itself — no cross-guards

In multi-pointer problems, **each pointer's safety chain must use its own name**. Don't mix:

```python
# BUG — slow's status doesn't protect fast
while slow and fast.next:        # ✗ slow being non-None says nothing about fast
                                  #   if fast is None, fast.next crashes

# OK — fast guards its own dereferences
while fast and fast.next:        # ✓ checks fast first, then fast.next
```

Python's `and` is short-circuit (left-to-right). The check **must walk the same object's chain in shallowest-first order**, so the early guards prevent the later dereferences from crashing during evaluation.

## Why the order matters: dereference-during-check trap

Naive: `while fast.next.next:` — looks like one check, but Python evaluates by accessing `fast.next.next` directly. If `fast.next` is None, `None.next` **crashes during the condition check itself**. The condition can't even return False — it explodes.

Fixed: split the chain:
```python
while fast and fast.next:    # progressive null-guards
    fast = fast.next.next    # safe to dereference inside body
```

Each `and` is a guard for the next deeper access. By the time you reach `fast.next.next` *inside the body*, you've already proven both `fast` and `fast.next` are non-None.

## Five mental frameworks for picking the guard

Use whichever sticks for your brain. They all converge on the same answer.

### 1. Reverse-engineer from the body
List every dereference inside the loop. Write the union of "what must be alive" for each. That's the guard.

```python
while ___:
    slow = slow.next         # needs: slow alive
    fast = fast.next.next    # needs: fast alive AND fast.next alive
```
Union: `fast and fast.next` (slow is implied — see "free rides" below).

### 2. Trace the crash without a guard
Pretend the loop has no condition (`while True`). Run on a small input. Where does it crash first? **That's** the missing check.

For `1 → 2 → None`: iteration 2 hits `fast.next` when fast is None → that's the missing guard.

### 3. Shallowest-first chain
For `a.b.c.d`, walk: `a`, then `a.b`, then `a.b.c`. Always shallowest first because Python's short-circuit relies on early failures preventing late dereferences.

### 4. Each variable guards itself (no cross-protection)
Don't use one pointer to guard another. `slow and fast.next` is wrong — slow's existence doesn't protect fast. Each pointer needs its own chain.

### 5. "Can we take another step?" (work-based)
Ask: what's the meaningful work this iteration? What state does that work require? Encode that as the condition.

For Floyd's: work = "fast takes 2 steps." Requires fast and fast.next both alive. Done.

## Free rides — when one pointer's guard implies another's

Sometimes you can drop a redundant check because one variable's safety is implied by another's. The classic case is **trailing pointers**.

**Floyd's:** slow trails fast (slow's position ≤ fast's position always, since slow moves 1 and fast moves 2). So if fast hasn't fallen off, slow can't have either. → no need to check slow in the condition.

**Merge two sorted lists:** list1 and list2 are independent — neither implies the other. Both must be in the guard: `while list1 and list2`.

**Reverse linked list with prev/curr/next:** `prev` and `next_node` are always behind/ahead of `curr`. The guard is just `while curr` because curr is the cursor doing all the dereferencing.

**Rule of thumb:** include only the pointer(s) whose dereference would crash. Skip pointers that trail or follow them.

## Worked examples

### Floyd's cycle detection (LC 141)
```python
while fast and fast.next:    # fast does fast.next.next; slow trails so it's safe
    slow = slow.next
    fast = fast.next.next
```

### Merge two sorted lists (LC 21)
```python
while list1 and list2:       # both deref .val; neither trails the other
    if list1.val <= list2.val:
        ...
```

### Reverse linked list (LC 206)
```python
while curr:                  # curr deref .next; that's the only cursor
    curr.next, prev, curr = prev, curr, curr.next
```

### Remove nth from end (LC 19) — two-phase guard
```python
# Phase 1: advance fast n steps
for _ in range(n):
    fast = fast.next
# Phase 2: advance both until fast.next is None
while fast.next:             # body uses fast.next, so guard fast.next directly
    slow = slow.next
    fast = fast.next
```

(Different problem, different deepest-deref, different guard. Always work backward from the body.)

## Common bugs

**Cross-guarding** — using one variable's status to "protect" another's deref:
```python
while slow and fast.next:    # ✗ slow's life doesn't guarantee fast's
```

**Shallowest-only** — checking only the outermost pointer:
```python
while fast:                  # ✗ when fast.next is None, fast.next.next crashes
    fast = fast.next.next
```

**Dereference-during-check** — putting deep deref directly in the condition:
```python
while fast.next.next:        # ✗ if fast.next is None, the check itself crashes
```

**Outdated guard** — writing the condition before the body (so it doesn't reflect the body's actual reads):
```python
while curr:                  # ✓ if body reads only curr.next
                              # ✗ if body reads curr.next.next (need curr and curr.next)
```

The guard must match what the **body actually does**, not what you originally planned.

## TL;DR (one line)

> **Look at the deepest dereference inside the loop body. Walk its parent chain shallowest-first. That chain joined by `and` is your loop condition. Don't cross-guard between variables.**
