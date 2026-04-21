# Sign handling — negate with math, not string concat

**TL;DR:** When a problem involves a signed integer via string manipulation, strip the sign upfront, do the work on unsigned digits, then **reapply via multiplication** (`-x` or `sign * x`). Don't rebuild a signed string with `"-" + str`.

**Patterns:** type_boundary, sign_handling, digit_manipulation

## The principle

> Convert types at the boundary. Do the work in the right type. Don't concatenate signs onto strings when you'll convert to int anyway.

Once you're in int-land, negation is one `-` operator. Once you're in string-land, "negation" means concat + reparse — more code, more bugs.

## Where it matters — with usage

### LC 7 Reverse Integer — flip sign AFTER int conversion
```python
# BAD — string concat for sign
reversed_str = num_str[::-1]
result = int("-" + reversed_str) if has_negative else int(reversed_str)
# relies on int() parsing "-", two int() calls

# GOOD — multiply after single int conversion
reversed_int = int(num_str[::-1])
if has_negative:
    reversed_int = -reversed_int                  # one operator, done
```

### LC 8 String to Integer (atoi) — sign tracked as multiplier
```python
def myAtoi(s):
    s = s.lstrip()
    if not s: return 0

    sign = 1
    i = 0
    if s[0] in "+-":
        if s[0] == "-": sign = -1
        i = 1

    result = 0
    while i < len(s) and s[i].isdigit():
        result = result * 10 + int(s[i])          # unsigned digit math
        i += 1

    result *= sign                                 # apply sign at the end
    return max(-2**31, min(2**31 - 1, result))    # clamp
```

### LC 29 Divide Two Integers — sign out, abs math, sign back
```python
def divide(dividend, divisor):
    sign = -1 if (dividend < 0) ^ (divisor < 0) else 1  # XOR = true if exactly one negative
    a, b = abs(dividend), abs(divisor)
    # ... do unsigned division via bit shifts ...
    return sign * quotient
```

### LC 50 Pow(x, n) — negative exponent as reciprocal
```python
def myPow(x, n):
    if n < 0:
        x = 1 / x
        n = -n                                     # flip sign once, proceed unsigned
    # ... fast exponentiation on positive n ...
```

### LC 166 Fraction to Recurring Decimal — sign tracked as string
```python
def fractionToDecimal(num, den):
    if num == 0: return "0"
    sign = "-" if (num < 0) ^ (den < 0) else ""
    num, den = abs(num), abs(den)
    # ... do the long division on unsigned numbers ...
    return sign + result                           # sign re-attached at the very end
```

## The three patterns

### Pattern 1 — sign as `-1 or 1` multiplier (cleanest for numeric ops)
```python
sign = -1 if x < 0 else 1
x = abs(x)
# ... work with positive x ...
return sign * result
```

### Pattern 2 — negate at the end if a flag is set
```python
has_negative = x < 0
x = abs(x)
# ... work with positive x ...
return -result if has_negative else result
```

### Pattern 3 — sign as string prefix (only for string OUTPUT)
```python
# use when the FINAL output is a string (LC 166, LC 43 multiply strings)
sign = "-" if (a < 0) ^ (b < 0) else ""
return sign + digits
```

## When to use each

| Output type | Pattern |
|---|---|
| int | multiplier (`sign * result`) |
| string | prefix (`"-" + result` if negative) |
| input is string, output is int | strip sign → int math → multiplier |

## Gotchas

**Gotcha 1 — don't mix patterns.** If you're returning an int, stay in int-land for the sign. Converting to string just to prepend `"-"` and then converting back is a code smell.

**Gotcha 2 — XOR for "exactly one is negative".** `(a < 0) ^ (b < 0)` is the idiom for division/multiplication sign. More concise than `(a < 0 and b > 0) or (a > 0 and b < 0)`.

**Gotcha 3 — `abs(INT_MIN)` overflows in 32-bit-constrained problems.** See `overflow-safe-arithmetic.md`. When you strip the sign of INT_MIN you can't represent the positive in int32. Handle as a special case or use Python's bignum.

**Gotcha 4 — `int("-" + "123")` works but `int("-")` throws.** If the string-prefix pattern is ever applied to an empty digit string, you get a ValueError. Defensive: check digits are present before prepending sign.
