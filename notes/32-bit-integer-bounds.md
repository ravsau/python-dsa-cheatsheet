# 32-bit integer bounds

**TL;DR:** `INT_MAX = 2**31 - 1`, `INT_MIN = -2**31`. The asymmetry (`-1` on MAX only) comes from zero living with non-negatives in two's complement.

**Where it matters:**
- LC 7 Reverse Integer — return 0 if reversed overflows INT32
- LC 8 String to Integer (atoi) — clamp to [INT_MIN, INT_MAX]
- LC 29 Divide Two Integers — `abs(INT_MIN)` itself overflows
- LC 50 Pow(x, n) — `abs(n)` when `n == INT_MIN` overflows

**Patterns:** overflow-detection, bounded-arithmetic

## Why 2^31 and not 2^32 — signed vs unsigned

32 bits store `2^32` distinct patterns (~4.3B). How you use them depends on the representation:

| Representation | Range | Max |
|---|---|---|
| Unsigned 32-bit | 0 to 2^32 − 1 (~4.3B) | `2^32 − 1` |
| **Signed 32-bit** | **−2^31 to 2^31 − 1** (~±2.1B) | **`2^31 − 1`** |

**Signed** splits the patterns in half — half for negatives, half for non-negatives. Each half = `2^32 / 2 = 2^31`. That's where the **31** (not 32) in the exponent comes from.

The additional **`− 1`** on MAX comes from zero counting as non-negative (zero lives on the non-negative side of the split).

**Breakdown of `2^31 − 1`:**
- `31` = signed (half the bits go to negatives)
- `− 1` = zero takes a slot on the non-negative side

INT_MIN gets only the `31` adjustment (`−2^31`), no `− 1` — the negative side doesn't have a zero to share with.

**The bit-pattern split** (32 bits → 2^32 patterns):
- Non-negatives: `0, 1, ..., 2^31 - 1` → 2^31 values *including 0* → max = count − 1
- Negatives: `-1, -2, ..., -2^31` → 2^31 values, no zero slot → min = −count

**Tiny example — 4-bit signed:**
| Interpretation | Range | Max |
|---|---|---|
| Unsigned 4-bit | 0 to 15 | `2^4 − 1 = 15` |
| Signed 4-bit | −8 to 7 | `2^(4−1) − 1 = 7` |

Same pattern scales to 32, 64, 128 bits — exponent always drops by 1 when going signed.

**Code:**
```python
INT_MAX = 2**31 - 1       #  2_147_483_647
INT_MIN = -2**31          # -2_147_483_648
# equivalently
INT_MAX, INT_MIN = (1 << 31) - 1, -(1 << 31)
```

**Gotcha:** `abs(INT_MIN) == 2**31`, which is **one bigger than INT_MAX**. In Java / C++ `Math.abs(Integer.MIN_VALUE)` silently returns `INT_MIN`. In Python it's fine at runtime (bignum) but LeetCode graders for LC 7/8/29 enforce 32-bit bounds — you must voluntarily cap.

**Mental model:** "non-negative vs negative", not "positive vs negative." Zero lives with non-negatives because `0 ≥ 0`.

## Which variable do you bounds-check — input or output?

**Rule:** the problem statement *guarantees* the input fits in 32-bit. The thing that might NOT fit is the **transformed value** (the reversed number, the parsed atoi result, the quotient). **Check the output, not the input.**

This sounds obvious but it's the single most common bug on overflow problems:

```python
# ❌ WRONG — input is already known to fit, this check is dead code
def reverse(self, x):
    if x > 2**31 - 1 or x < -2**31:
        return 0                          # never fires; x is given to fit
    ...build result by reversing digits...
    return result                          # ← THIS may overflow, unchecked

# ✅ RIGHT — check the value that can actually overflow
def reverse(self, x):
    ...build result by reversing digits...
    if result > 2**31 - 1 or result < -2**31:
        return 0
    return result
```

**Trace the canonical failure case:** `x = 1534236469` is a valid 32-bit input. Reversed it's `9646324351`, which is `> 2^31 - 1`. Expected return is `0`. If your check was on `x` (input), you'd skip the bail and return the overflowed value — wrong answer.

**Where to place the check:**

| Placement | When |
|---|---|
| **After the loop, before return** | Easiest; works in Python because ints don't actually overflow. Ship this if interviewer says "Python is fine." |
| **Inside the loop, *before* the multiply** | "32-bit-safe" canonical form. Required when the language actually overflows (C/C++/Java) or when interviewer says "no 64-bit intermediates." See `overflow-safe-arithmetic.md`. |
| **Before the loop (on input)** | ❌ Almost always wrong — input is bounded by problem statement. Dead code. |

**Generalized principle:** in any problem with a bounded input domain and an unbounded output, the bounds check belongs on the **output side of the transformation**. Same shape applies to:
- LC 7 — input ∈ INT32, reversed value can overflow
- LC 8 (atoi) — input string can encode any number, parsed int must clamp
- LC 29 (divide) — `abs(INT_MIN)` overflows during transformation, even though both inputs fit
- LC 50 (pow) — `abs(n)` when `n == INT_MIN` overflows
- LC 43 (multiply strings) — inputs are strings (no bound), product clamping is on the output

When you read a new overflow problem, ask: **"what's the input domain, and what's the function-of-input domain?"** Bound-check goes on the side that's bigger.
