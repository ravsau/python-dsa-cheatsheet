# 32-bit integer bounds

**TL;DR:** `INT_MAX = 2**31 - 1`, `INT_MIN = -2**31`. The asymmetry (`-1` on MAX only) comes from zero living with non-negatives in two's complement.

**Where it matters:**
- LC 7 Reverse Integer — return 0 if reversed overflows INT32
- LC 8 String to Integer (atoi) — clamp to [INT_MIN, INT_MAX]
- LC 29 Divide Two Integers — `abs(INT_MIN)` itself overflows
- LC 50 Pow(x, n) — `abs(n)` when `n == INT_MIN` overflows

**Patterns:** overflow-detection, bounded-arithmetic

**The bit-pattern split** (32 bits → 2^32 patterns):
- Non-negatives: `0, 1, ..., 2^31 - 1` → 2^31 values *including 0* → max = count − 1
- Negatives: `-1, -2, ..., -2^31` → 2^31 values, no zero slot → min = −count

**Code:**
```python
INT_MAX = 2**31 - 1       #  2_147_483_647
INT_MIN = -2**31          # -2_147_483_648
# equivalently
INT_MAX, INT_MIN = (1 << 31) - 1, -(1 << 31)
```

**Gotcha:** `abs(INT_MIN) == 2**31`, which is **one bigger than INT_MAX**. In Java / C++ `Math.abs(Integer.MIN_VALUE)` silently returns `INT_MIN`. In Python it's fine at runtime (bignum) but LeetCode graders for LC 7/8/29 enforce 32-bit bounds — you must voluntarily cap.

**Mental model:** "non-negative vs negative", not "positive vs negative." Zero lives with non-negatives because `0 ≥ 0`.
