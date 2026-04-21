# ord/chr alphabet indexing

**TL;DR:** `ord(c) - ord('a')` maps lowercase letters to 0..25. Replaces a dict with a `[0]*26` array — faster, cleaner, classic.

**Where it matters:**
- LC 49 Group Anagrams — key = tuple of 26-element count array
- LC 242 Valid Anagram — compare count arrays
- LC 383 Ransom Note — count chars in magazine
- LC 567 Permutation in String — sliding window of char counts
- LC 438 Find All Anagrams in a String — same pattern
- LC 771 Jewels and Stones — set membership
- LC 387 First Unique Character — count then scan
- LC 208 Trie — `children[26]` array instead of dict

**Patterns:** anagram, frequency, trie, fixed_alphabet

**Code:**
```python
count = [0] * 26
for c in s:
    count[ord(c) - ord('a')] += 1     # lowercase only

# back to char
c = chr(i + ord('a'))                  # 0→'a', 25→'z'
```

**For mixed case / digits:**
```python
ord('A')  # 65      ord('a')  # 97      ord('0')  # 48
# full printable ASCII: count = [0] * 128
# just uppercase: count[ord(c) - ord('A')]
# digits: count[ord(c) - ord('0')]
```

**Anagram check — two styles:**
```python
# style A — count array (O(n) time, O(1) space since 26 is fixed)
def is_anagram(s, t):
    if len(s) != len(t): return False
    count = [0] * 26
    for cs, ct in zip(s, t):
        count[ord(cs) - ord('a')] += 1
        count[ord(ct) - ord('a')] -= 1
    return all(x == 0 for x in count)

# style B — Counter (more Pythonic, slightly slower)
from collections import Counter
def is_anagram(s, t):
    return Counter(s) == Counter(t)
```

**Group Anagrams key (LC 49):**
```python
def key(s):
    count = [0] * 26
    for c in s: count[ord(c) - ord('a')] += 1
    return tuple(count)        # tuple is hashable, list is not
```
This is O(n) per key vs O(n log n) for `"".join(sorted(s))`.

**Complexity:** array access O(1), 26-size scans O(1) since 26 is constant. Use count arrays when alphabet is bounded — they're asymptotically and practically faster than dicts.

**Gotcha 1 — case sensitivity.** Uppercase `'A'` has ord 65, `'a'` has ord 97 — **32 apart**, not adjacent. Calling `ord(c) - ord('a')` on uppercase gives negative indices → silent bug.

**Gotcha 2 — non-ASCII.** `ord('é') = 233`, way outside `[0, 26)`. Validate input or use Counter for unicode.

**Gotcha 3 — can't use list as dict key.** `count` is a list; convert to `tuple(count)` before using as a dict key (LC 49 trap).
