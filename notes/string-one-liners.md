# String / number one-liners

**TL;DR:** The stdlib one-liners below replace 3-4 line loops on half the string/parsing problems. Memorize them.

**Where it matters:** all over strings/parsing — LC 20, 125, 242, 387, 1768, 14, 796, and many more.

**Patterns:** string_parsing, character_checks, base_conversion

## Character classification
```python
c.isdigit()           # '0'-'9'
c.isalpha()           # letters only (locale-aware)
c.isalnum()           # alphanumeric
c.isspace()           # whitespace
c.isupper()           # uppercase
c.islower()           # lowercase
c.isascii()           # ASCII only (Py 3.7+)
```
Use in: LC 125 Valid Palindrome (`if c.isalnum()`), LC 14 Longest Common Prefix.

## Case / whitespace
```python
s.lower()   s.upper()   s.swapcase()   s.title()
s.strip()   s.lstrip()   s.rstrip()
s.strip("xyz")         # strip specific chars, not just whitespace
```

## Split / join
```python
s.split()              # default: splits on ANY whitespace, drops empties
s.split(",")           # split on comma
s.split(" ")           # split on single space — keeps empty strings
s.rsplit(",", 1)       # split from right, at most 1 split
"".join(words)         # concat
",".join(words)        # comma-separated
```
Use in: LC 151 Reverse Words, LC 71 Simplify Path.

## Search / count
```python
s.find("xyz")          # -1 if not found
s.index("xyz")         # ValueError if not found
s.count("a")           # occurrences
s.startswith("http")   # prefix check
s.endswith(".py")      # suffix check
```
Use in: LC 28 strStr (`s.find`), LC 771 Jewels and Stones (`s.count`).

## Replace
```python
s.replace("a", "b")           # replace all
s.replace("a", "b", 1)        # at most 1 replacement
```

## Number ↔ string
```python
int("123")             # 123
int("ff", 16)          # 255 (base-16)
int("101", 2)          # 5 (binary)
bin(5)                 # '0b101'
hex(255)               # '0xff'
oct(8)                 # '0o10'
bin(5)[2:]             # '101' (strip '0b')
```
Use in: LC 67 Add Binary (`bin(int(a,2)+int(b,2))[2:]`), LC 191 Number of 1 Bits (`bin(n).count('1')`).

## Math essentials
```python
abs(x)                     # absolute value
pow(b, e)                  # b**e
pow(b, e, m)               # (b**e) % m — modular exponentiation, FAST
divmod(a, b)               # (a // b, a % b)
min(a, b, c)    max(a, b, c)
min(arr, key=...)          # min by custom key
```
Use in: LC 50 Pow(x, n) (variant), LC 372 Super Pow (`pow(a, b, mod)`), LC 539 Min Time Difference.

## GCD / LCM
```python
from math import gcd, lcm              # Py 3.5+ / 3.9+
gcd(12, 18)                            # 6
lcm(4, 6)                              # 12
from functools import reduce
reduce(gcd, arr)                       # GCD of entire list
reduce(lcm, arr)                       # LCM of entire list
```
Use in: LC 1979 GCD of Array, LC 592 Fraction Addition.

## Formatting
```python
f"{x:04d}"             # zero-pad: '0005'
f"{x:.2f}"             # 2 decimal places
f"{x:,}"               # thousands separator: '1,000,000'
f"{x:b}"               # binary
f"{x:x}"               # hex
"*".join(str(n) for n in nums)
```

**Gotcha 1 — `find` vs `index`.** `find` returns -1 on miss; `index` raises. Pick `find` when absence is expected.

**Gotcha 2 — `split()` vs `split(" ")`.** No arg: splits on any whitespace run, drops empty. With `" "`: splits on each single space, keeps empties. `"a  b".split()` → `["a","b"]`; `"a  b".split(" ")` → `["a","","b"]`.

**Gotcha 3 — `pow(b, e, m)` is MUCH faster than `(b**e) % m`.** For huge exponents (crypto-scale), use the 3-arg form — built-in modular exponentiation in C.

**Gotcha 4 — strings are immutable.** `s[0] = 'x'` → TypeError. To mutate, convert to list: `l = list(s); l[0] = 'x'; s = "".join(l)`.
