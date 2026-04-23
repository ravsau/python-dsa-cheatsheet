# Inclusive vs exclusive bounds — the `+1` in window length

**TL;DR:** When `l` and `r` are both indices **inside** a window, its length is `r - l + 1`. When `r` is "one past the end" (Python slice / range convention), it's `r - l`. Know which style you're using.

**Patterns:** sliding_window, fencepost, off_by_one

## The rule

| Bound style | Notation | Length |
|---|---|---|
| Both inclusive | `[l, r]` | `r - l + 1` |
| Right exclusive | `[l, r)` | `r - l` |

The `+1` appears because subtracting two indices counts the **gaps between them**, not the **elements covered**.

## Fencepost mental model

```
indices:  0   1   2   3
houses:   H   H   H   H     ← 4 houses
gaps:       1   2   3       ← 3 gaps between them

  r - l   = gaps     = 3
(r-l) + 1 = houses   = 4
```

When both `l` and `r` are actual indices, you're counting **houses** — needs `+1`.

## Where it matters — with usage

### LC 3 Longest Substring Without Repeating — inclusive window
```python
# l and r are both valid indices inside the window
char_set = set()
l = 0
best = 0
for r in range(len(s)):
    while s[r] in char_set:
        char_set.remove(s[l])
        l += 1
    char_set.add(s[r])
    best = max(best, r - l + 1)          # +1 because [l, r] is inclusive both sides
return best
```

### LC 76 Minimum Window Substring — inclusive window
```python
if r - l + 1 < best_len:                  # same inclusive convention
    best_len = r - l + 1
    best_start = l
# to extract the window at the end: s[best_start : best_start + best_len]
```

### LC 209 Minimum Size Subarray Sum — inclusive
```python
best = min(best, r - l + 1)
```

### LC 424 Longest Char Replacement — inclusive
```python
best = max(best, r - l + 1)
```

### Python-slice convention (exclusive right) — no `+1`
```python
# if you store the window as s[l:r], r is ONE PAST the last included index
window = s[l:r]
window_len = r - l                        # NO +1 here
# because r itself is not a valid char in the window
```

### range() is also exclusive-right
```python
len(range(0, 5))   # 5, not 4 + 1 = 5. range is [0, 5), that's 5 numbers.
```

## When do you add `+1`?

Short decision:
- Is `r` a valid index pointing to a character you care about? → `r - l + 1`
- Is `r` "one past" the last valid index (Python-style)? → `r - l`

If you're confused mid-problem: **trace a length-3 example by hand** and see which formula gives 3.

## Pick a style and stick with it

Don't mix inclusive and exclusive within the same function. Common templates:

**Style A — both inclusive (sliding window default):**
```python
l = 0
for r in range(len(s)):                   # r is a real index
    # window is [l, r], length r - l + 1
    ...
```

**Style B — exclusive right (Python slice style):**
```python
l = 0
r = 0                                     # r is the first index NOT in window
while r < len(s):
    # window is s[l:r], length r - l
    r += 1
```

Most sliding-window solutions on LC use **Style A** because `for r in range(len(s))` reads cleaner — but then you owe the `+1` everywhere you compute length. Miss it and you're off by one.

## Gotchas

**Gotcha 1 — mixing styles silently.** Computing `r - l` in a for-loop where `r` is a valid index gives you the length minus one. Bug manifests as "answer is 1 short."

**Gotcha 2 — empty window.** Inclusive `[l, r]` with `l > r` represents an empty window (length 0). Inclusive `[l, l]` is length 1 (the single element at index `l`). Guard your reduce / max / min operations against empty-window states if they can occur.

**Gotcha 3 — extraction.** If you tracked `(best_l, best_r)` inclusive-both, extract with `s[best_l : best_r + 1]`. The `+1` comes back because Python slicing is exclusive-right. Forgetting this truncates your output by one char.

**Gotcha 4 — range(l, r+1) vs range(l, r).** `range(l, r+1)` hits index `r`. `range(l, r)` stops at `r-1`. Pick based on whether `r` is in your inclusive window.
