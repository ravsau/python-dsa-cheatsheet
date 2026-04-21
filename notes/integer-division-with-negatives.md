# Integer division with negatives

**TL;DR:** Python `//` floors toward negative infinity. `-123 // 10 == -13`, not `-12`. And `-123 % 10 == 7`, not `-3`.

**Where it matters:**
- LC 7 Reverse Integer — digit extraction breaks on negatives
- LC 29 Divide Two Integers — sign handling
- LC 166 Fraction to Recurring Decimal — remainder tracking
- LC 50 Pow(x, n) — fast exponentiation with negative exponent

**Patterns:** digit-extraction, modulo-tricks

**The invariant Python enforces:** `dividend = divisor * quotient + remainder`, with remainder's sign matching the *divisor*.

For `-123 % 10`: quotient = `-13`, so `-123 = 10*(-13) + r` → `r = 7`.

**Compare languages:**

| Language | `-123 // 10` | `-123 % 10` | Rule |
|---|---|---|---|
| Python, Ruby, Haskell | -13 | 7 | floor (sign follows divisor) |
| C, C++, Java, JS, Go, Rust | -12 | -3 | truncate (sign follows dividend) |

**Code — safe digit extraction:**
```python
sign = -1 if x < 0 else 1
x = abs(x)
while x > 0:
    digit = x % 10        # now always 0..9
    x //= 10
    # ...build result...
return sign * result
```

**Gotcha:** If you port a C solution to Python verbatim, digit extraction from negatives silently corrupts. Strip sign upfront or use `int(math.fmod(x, 10))` + `int(x / 10)` to get C-style semantics.
