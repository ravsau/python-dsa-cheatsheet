# Character classification — `isX` methods

**TL;DR:** Python strings have built-in predicate methods (`isalnum`, `isdigit`, `isalpha`, etc.) that test what kind of character you have. Combined with `"".join(filter)` they replace 5-line loops with one expression.

**Patterns:** string_filtering, character_test, parsing

## The full family

| Method | True for | Example |
|---|---|---|
| `c.isalpha()` | letters only (a-z, A-Z) | `'a'.isalpha()` → True; `'5'.isalpha()` → False |
| `c.isdigit()` | digits 0-9 | `'5'.isdigit()` → True; `'a'.isdigit()` → False |
| **`c.isalnum()`** | **letters OR digits** | `'a'.isalnum()` → True; `' '.isalnum()` → False |
| `c.isspace()` | whitespace (space, tab, newline) | `' '.isspace()` → True |
| `c.isupper()` | uppercase letter | `'A'.isupper()` → True; `'a'.isupper()` → False |
| `c.islower()` | lowercase letter | `'a'.islower()` → True; `'A'.islower()` → False |
| `c.isascii()` | ASCII range only (Py 3.7+) | `'é'.isascii()` → False |
| `c.isnumeric()` | digits + numeric chars (²,½,etc.) | rare |
| `c.isdecimal()` | strict decimal digits | rare |

These work on **any string**, not just single chars — but interviews mostly use them on chars in a loop.

## Where it matters — with usage

### LC 125 Valid Palindrome — filter alphanumerics
```python
def isPalindrome(s):
    clean = "".join(c.lower() for c in s if c.isalnum())   # filter + transform + join
    return clean == clean[::-1]
```

### LC 14 Longest Common Prefix — character iteration with type check
```python
# (not isX directly, but demonstrates the idiom)
```

### LC 387 First Unique Character — iterate chars
```python
def firstUniqChar(s):
    count = Counter(s)
    for i, c in enumerate(s):
        if count[c] == 1:
            return i
    return -1
```

### LC 8 String to Integer (atoi) — digit detection
```python
def myAtoi(s):
    s = s.lstrip()
    if not s: return 0
    i, sign = 0, 1
    if s[0] in "+-":
        if s[0] == "-": sign = -1
        i = 1
    result = 0
    while i < len(s) and s[i].isdigit():     # ← isdigit gate
        result = result * 10 + int(s[i])
        i += 1
    return sign * result
```

### LC 165 Compare Version Numbers — split + digit work
```python
def compareVersion(v1, v2):
    parts1 = [int(p) for p in v1.split(".")]
    parts2 = [int(p) for p in v2.split(".")]
    # ...
```

### LC 1768 Merge Strings Alternately — character-by-character
```python
def mergeAlternately(word1, word2):
    return "".join(a + b for a, b in zip_longest(word1, word2, fillvalue=""))
```

### Common: count of each type in a string
```python
digits = sum(1 for c in s if c.isdigit())
letters = sum(1 for c in s if c.isalpha())
spaces = sum(1 for c in s if c.isspace())
```

## The "filter + transform + join" idiom

```python
"<sep>".join(transform(x) for x in iterable if filter(x))
```

Three jobs in one expression. Common shapes:

```python
"".join(c for c in s if c.isalpha())           # remove non-letters
"".join(c.lower() for c in s if c.isalnum())   # alphanum only, lowered
", ".join(str(n) for n in nums if n > 0)       # CSV of positive nums
" ".join(w.capitalize() for w in s.split())    # Title Case
"".join(c for c in s if c != " ")              # remove spaces
```

**Equivalent unrolled (always works):**
```python
out = []
for x in iterable:
    if filter(x):
        out.append(transform(x))
result = sep.join(out)
```

## When to use char tests vs regex

`c.isalnum()` is **faster and more readable** than `re.match(r'[a-zA-Z0-9]', c)` for single-char checks. Use regex only when:
- Patterns span multiple chars (`r'\d{3}'`)
- You need find/replace across whole strings
- Complex grammars (URLs, dates)

For "is this character a letter?" → always `isX` methods.

## Gotchas

**Gotcha 1 — empty string is False.** `"".isalnum()` returns False, not True (vacuous-truth False). Be careful in chained checks.

**Gotcha 2 — unicode is included.** `'é'.isalpha()` is True. `'5'.isdigit()` is True for unicode digits like `'٥'` (Arabic 5) too. If you need ASCII-strict, combine: `c.isalnum() and c.isascii()`.

**Gotcha 3 — `isnumeric` vs `isdigit` vs `isdecimal`.** All three exist. Differences:
- `isdecimal()` — strict 0-9 (and unicode equivalents like Arabic-Indic)
- `isdigit()` — adds super/subscript digits like ²
- `isnumeric()` — adds fractions like ½, Roman numerals
For interviews, `isdigit()` is what you want 99% of the time.

**Gotcha 4 — `'A'.lower()` returns `'a'`, but `c.lower()` doesn't mutate `c`.** Strings are immutable. The result has to be assigned: `c = c.lower()` or used in a comprehension.

**Gotcha 5 — `str.lower()` works on whole strings too.** `"HeLLo".lower()` → `"hello"`. Don't write `"".join(c.lower() for c in s)` when you can write `s.lower()`. Only the comprehension form is needed when you're ALSO filtering.

## Cross-references

- `string-one-liners.md` — broader stdlib string ops
- `set-algebra.md` — set membership for `c in vowels` style checks
