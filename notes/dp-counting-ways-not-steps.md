# DP — you're counting ways, not steps

**TL;DR:** `dp[i]` is a **value** stored at slot `i`, not the step number itself. The index is just a label (where you are); the value is what you're computing (count of ways, max profit, min cost, etc.). Sum/max/min operates on **values**, never on indices.

**Where it matters:** every DP problem you'll ever write — but especially the FIRST one.
- LC 70 Climbing Stairs — `dp[i]` = count of ways to reach step i
- LC 198 House Robber — `dp[i]` = max money robbing first i houses
- LC 322 Coin Change — `dp[i]` = min coins to make amount i
- LC 139 Word Break — `dp[i]` = True/False if `s[:i]` is segmentable
- LC 1143 LCS — `dp[i][j]` = longest common subsequence of `a[:i]` and `b[:j]`

**Patterns:** dp_1d, dp_2d, mental_model, beginner

## The mental model

A `dp` array is a **bookkeeping ledger**, not a map of locations.

```
slot index:    0   1   2   3   4   5
dp[i]:         ?   1   2   3   5   8       ← values being computed/stored
                       ↑   ↑   ↑
                       └───┴───┴── each slot stores ONE answer for ONE subproblem
```

For LC 70:
- The **slot** at index 3 represents "step 3 of the staircase" (a location)
- The **value** in that slot, `dp[3] = 3`, is "the count of distinct ways to reach step 3"

These are **two different things**:
- The slot label = WHERE
- The slot value = HOW MANY ways / WHAT BEST value / WHETHER possible

When the recurrence says `dp[i] = dp[i-1] + dp[i-2]`, the `+` operates on **values**, not on slot labels. You're adding two counts (or two max-values, or two whatever-values), not two step numbers.

## Beginner's confusion (and the fix)

Most people stuck on their first DP problem are confused about this exact thing:

> *"How does `dp[3] + dp[2] = 5` give us `dp[4]`? Step 3 + step 2 ≠ step 4!"*

The fix: **you're not adding step numbers. You're adding the values stored at those slots.** The labels (3 and 2) are just where the values live; they're irrelevant to the arithmetic.

Library shelf analogy:
```
shelf 2 has 2 books
shelf 3 has 3 books

If we're moving books FROM shelves 2 and 3 ONTO shelf 4...
shelf 4 ends up with: 2 + 3 = 5 books

We're not adding "shelf 2 + shelf 3" (that's nonsensical).
We're adding the COUNTS OF BOOKS on each shelf.
```

`dp[3] + dp[2]` does the same thing — it sums **values** (counts of paths), and the sum becomes the value stored at slot 4.

## The recurrence in plain English

For LC 70:
```python
dp[i] = dp[i-1] + dp[i-2]
```

Read this as:

> "**Number of ways to reach step i** = (**ways to reach step i-1**, then take a 1-step) + (**ways to reach step i-2**, then take a 2-step)."

Notice the parts in bold are **counts of paths** — and the `+` connects two such counts. The "take a 1-step" / "take a 2-step" extensions don't add to the count because each is a single free move (one 1-step is one specific extension; doesn't multiply).

## The same model with different value types

The "label vs value" distinction holds whether `dp[i]` is a count, a max, a min, or a boolean:

```python
# COUNTING (LC 70)
dp[i] = dp[i-1] + dp[i-2]                     # sum of counts

# MAX (LC 198 — rob house i or skip)
dp[i] = max(dp[i-1], dp[i-2] + nums[i])       # best value of two choices

# MIN (LC 322 — coins to make amount i)
dp[i] = min(dp[i-coin] + 1 for coin in coins) # best (lowest) value over choices

# BOOLEAN (LC 139 — is s[:i] segmentable)
dp[i] = any(dp[j] and s[j:i] in wordDict      # OR over sub-decisions
            for j in range(i))
```

Same structure, different operator on the values. The index `i` is always just a label; the value is what's being computed.

## How to debug your understanding

When you see `dp[i] = SOMETHING`, ask three questions:

1. **What does the SLOT (index `i`) represent?**  
   E.g., "step `i` of the staircase," "first `i` houses," "amount `i` to make change for"

2. **What is the VALUE at that slot?**  
   E.g., "count of ways," "max money," "min coins," "True/False"

3. **What's the recurrence saying about the value?**  
   E.g., "value at i = sum of values at i-1 and i-2"

If you can answer all three for a problem, you've got the DP mental model. If you can't, that's where the gap is — fix it before writing code.

## Common bug — confusing index vs value

```python
# Subtle bug — using index where you mean value
dp[i] = i + dp[i-1]              # ❌ "step number" added — almost always wrong

# Correct (LC 70)
dp[i] = dp[i-1] + dp[i-2]        # ✓ adds VALUES from two slots

# Correct (LC 198)
dp[i] = max(dp[i-1], dp[i-2] + nums[i])    # ✓ values + element value, never index
```

If `i` (the raw index) appears in your recurrence, double-check: is the problem actually asking for arithmetic on positions? Almost never.

## TL;DR (one line)

> **`dp[i]` is a value stored at slot i, not the step itself. Recurrences combine values from prior slots — the indices are just where the values live, not numbers being added.**

Once you see this, every DP problem reduces to: *"What's the slot, what's the value, what's the recurrence on the values?"* Three questions. Same shape every time.
