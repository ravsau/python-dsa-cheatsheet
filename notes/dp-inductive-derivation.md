# DP — the inductive method for deriving recurrences

**TL;DR:** Solve the trivial base cases first, then **derive the recurrence by computing the next case (usually `dp[2]`) from those bases**. The recurrence falls out of the work — you don't have to guess it.

**Where it matters:** every DP problem you'll write — but especially the FIRST time you see a new variant.
- LC 70 Climbing Stairs — derive `dp[i] = dp[i-1] + dp[i-2]` from `dp[0]=1, dp[1]=1, dp[2]=?`
- LC 198 House Robber — derive `dp[i] = max(nums[i] + dp[i-2], dp[i-1])` from `dp[0]=nums[0], dp[1]=max(nums[0],nums[1]), dp[2]=?`
- LC 322 Coin Change — derive `dp[i] = min(dp[i-c]+1 for c in coins)` from `dp[0]=0, dp[1]=?`
- LC 53 Max Subarray, LC 91 Decode Ways, LC 139 Word Break — same script

**Patterns:** dp_derivation, problem_solving_method, beginner

## The 3-step script

### Step 1 — Solve smallest cases by hand

For the trivial inputs (n=0, n=1, sometimes n=2), what's the answer? Compute it without using any "method" — just direct reasoning.

```python
# LC 198 example
dp[0] = nums[0]                  # only one house — must rob it
dp[1] = max(nums[0], nums[1])    # two adjacent houses — can only rob one, pick bigger
```

### Step 2 — Compute the FIRST non-trivial case (`dp[2]`)

Pick the smallest `i` that uses the recurrence (usually i=2). Compute `dp[2]` by **case-splitting on the last decision**.

```python
# LC 198 — at house 2, what are my options?
# OPTION A — rob house 2:
#   I collect nums[2]
#   But I can't have robbed house 1 (adjacent rule)
#   So I take the best up through house 0: dp[0]
#   Total = nums[2] + dp[0]
#
# OPTION B — skip house 2:
#   I don't collect nums[2]
#   I carry forward the best through house 1: dp[1]
#
# Take the better option:
dp[2] = max(nums[2] + dp[0], dp[1])
```

The work of figuring out `dp[2]` IS the work of figuring out the recurrence. You're not theorizing — you're computing.

### Step 3 — Generalize from `2` to `i`

The expression you derived for `dp[2]` works for ANY `i ≥ 2`. Just replace the `2`:

```python
# LC 198 — generalized
dp[i] = max(nums[i] + dp[i-2], dp[i-1])
```

That's the recurrence. Loop from `i = 2` to `n-1` filling in `dp[i]` using this formula.

## Why this method works

The recurrence captures **the relationship between dp[i] and earlier dp values**. By computing dp[2] manually, you're literally exercising that relationship on a concrete instance. The relationship doesn't change as `i` grows — only the specific index numbers do. So generalizing index `2 → i` gives you the universal rule.

It's the inductive proof structure applied to algorithm design:
1. **Base case** — the smallest cases work
2. **Inductive step** — given dp[k] for all k < i, compute dp[i]
3. **Conclude** — by induction, dp[n-1] is correct

## Worked examples

### LC 70 Climbing Stairs

```python
# Step 1: bases
dp[1] = 1   # one way to reach step 1 (single 1-step)
dp[2] = 2   # two ways to reach step 2 (1+1, or 2)

# Step 2: derive dp[3]
# At step 3, my last move was either a 1-step (from step 2) or a 2-step (from step 1)
# These are disjoint groups; total = sum of group counts
dp[3] = dp[2] + dp[1] = 2 + 1 = 3

# Step 3: generalize
dp[i] = dp[i-1] + dp[i-2]
```

### LC 198 House Robber

```python
# Step 1: bases
dp[0] = nums[0]
dp[1] = max(nums[0], nums[1])

# Step 2: derive dp[2]
# At house 2: rob it or skip it (mutually exclusive)
# Rob: nums[2] + dp[0]   (must skip i-1, take best through i-2)
# Skip: dp[1]            (just carry forward)
# We're maximizing, so take max
dp[2] = max(nums[2] + dp[0], dp[1])

# Step 3: generalize
dp[i] = max(nums[i] + dp[i-2], dp[i-1])
```

### LC 322 Coin Change

```python
# Step 1: bases
dp[0] = 0   # zero coins needed to make amount 0

# Step 2: derive dp[1] (or dp[smallest_coin])
# To make amount 1, I need to use SOME coin c such that I can make (1 - c)
# For each valid coin c: dp[1] could be dp[1 - c] + 1
# Take the minimum across all valid coins
dp[1] = min(dp[1 - c] + 1 for c in coins if c <= 1)

# Step 3: generalize
dp[i] = min(dp[i - c] + 1 for c in coins if c <= i)
```

Three different problems, three different recurrences (`+`, `max`, `min`), but the **derivation method is identical**.

## When to use this vs reading the recurrence somewhere

If you've never seen a problem before: **always use the inductive method.** Don't try to memorize recurrences from books or videos. The method makes you derive them, which is the actual interview skill.

If you've seen a problem before: you might recognize the recurrence pattern and write it from memory. But if you're unsure, fall back to the method — it always works.

## The pairing with tracing

This method is the natural complement to **trace-to-debug**:

- **Inductive derivation** → derive the recurrence by computing concrete cases
- **Trace-to-debug** → catch bugs in your code by walking concrete inputs

Both rely on the same fundamental skill: **work concrete examples by hand**, don't theorize abstractly. The `+1` fencepost in LC 209 was caught by tracing. The `max` in LC 198 was derived by inducing. Same skill, two applications.

## Common mistakes when deriving

**1. Skipping the manual bases.** Don't just write `dp = [0] * n` and start coding. Pause, write `dp[0] = ?` on paper, fill it in. Then `dp[1] = ?`. The bases anchor the recurrence.

**2. Computing dp[2] without case-splitting.** "What does dp[2] equal?" → not enough. Ask "**at index 2, what was my last decision/move/choice**?" Enumerate the options, then compute each one's contribution. The recurrence comes from comparing/combining those options.

**3. Skipping the generalization step.** You derived `dp[2] = max(nums[2] + dp[0], dp[1])`. Stop and **rewrite it with `i` instead of `2`** before coding. Verify the indices `i-2` and `i-1` make sense in the abstract.

## TL;DR (one line)

> **For any DP problem: solve dp[0], dp[1] by hand. Compute dp[2] by case-splitting on the last decision. Generalize 2 → i. The work IS the recurrence.**
