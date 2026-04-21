# Built-in vs hand-roll — when Python one-liners cost you signal

**TL;DR:** Use the built-in when the **mechanic isn't what's being tested**. Hand-roll when the mechanic IS the problem. When ambiguous, use the one-liner and verbally offer the hand-rolled version.

**Patterns:** interview_signaling, tradeoff_communication

## The rule

> Is the operation the one-liner replaces the POINT of the problem, or incidental to it?

- POINT → hand-roll it. Signal = "I understand the mechanic."
- INCIDENTAL → use built-in. Signal = "I'm allocating time to what matters."

## Where it matters — with decision

### LC 344 Reverse String — HAND-ROLL
```python
# problem: "reverse in place, O(1) extra space"
# the reversal IS the problem
def reverseString(s):
    l, r = 0, len(s) - 1
    while l < r:
        s[l], s[r] = s[r], s[l]
        l += 1; r -= 1
# s[:] = s[::-1]  also passes but loses the two-pointer signal
```

### LC 7 Reverse Integer — USE BUILT-IN
```python
# problem: reverse digits AND handle 32-bit overflow without 64-bit
# reversal is incidental — overflow check is the signal
def reverse(x):
    INT_MAX, INT_MIN = 2**31 - 1, -2**31
    sign = -1 if x < 0 else 1
    reversed_num = sign * int(str(abs(x))[::-1])          # one-liner reversal
    return 0 if reversed_num > INT_MAX or reversed_num < INT_MIN else reversed_num
```

### LC 206 Reverse Linked List — HAND-ROLL (no choice anyway)
```python
# problem: pointer manipulation
# no built-in exists, and even if it did, they want to see rewiring
def reverseList(head):
    prev = None
    while head:
        head.next, prev, head = prev, head, head.next
    return prev
```

### LC 125 Valid Palindrome — USE BUILT-IN (borderline)
```python
# problem: is this a palindrome?
# canonical Pythonic answer is the slice; interviewers accept it
def isPalindrome(s):
    cleaned = "".join(c.lower() for c in s if c.isalnum())
    return cleaned == cleaned[::-1]
```

### LC 151 Reverse Words in String — USE BUILT-IN
```python
# problem: parsing whitespace, word boundaries
# the "reverse" is trivial once you've split
def reverseWords(s):
    return " ".join(s.split()[::-1])
```

### LC 242 Valid Anagram — USE BUILT-IN
```python
# problem: equivalence of character multisets
# Counter equality is the canonical answer
def isAnagram(s, t):
    return Counter(s) == Counter(t)
    # sorted(s) == sorted(t) also acceptable — both one-liners
```

### LC 49 Group Anagrams — USE BUILT-IN + HAND-ROLL key
```python
# problem: grouping by anagram class
# grouping via defaultdict is built-in, but the KEY choice shows algorithm thinking
def groupAnagrams(strs):
    groups = defaultdict(list)
    for s in strs:
        # hand-rolled O(n) key (vs O(n log n) sorted(s))
        key = tuple(count := [0]*26) and tuple(count)
        for c in s: count[ord(c) - ord('a')] += 1
        groups[tuple(count)].append(s)
    return list(groups.values())
```

## Decision rubric

| Question | If YES | If NO |
|---|---|---|
| Does problem title describe the operation? (e.g. "Reverse...", "Sort...") | hand-roll | built-in |
| Does problem say "in place" or "O(1) space"? | hand-roll | built-in |
| Is the hard signal somewhere else (overflow, parsing, logic)? | built-in | consider hand-roll |
| Would a senior engineer hand-roll this in production? | hand-roll | built-in |

## The senior interview move

When ambiguous, say:

> "I'll use `[::-1]` here for brevity — O(n) time, O(n) space. If you'd prefer I show the two-pointer in-place version, that's O(n) time, O(1) space. For this problem the reversal isn't the bottleneck — I want to spend time on [the overflow check / edge cases / the hard part]. OK to proceed?"

One sentence tells the interviewer:
- You know both implementations exist
- You understand the complexity tradeoff
- You're prioritizing the real signal
- You're inviting redirect if they disagree

This is a **cracked-tier signal** — it shows meta-awareness of what the interview is testing, not just problem-solving.

## Gotchas

**Gotcha 1 — blind built-in use costs senior signal.** A junior uses `sorted(arr)`; a senior says "I'll use `sorted` — O(n log n), Timsort. If you want me to implement quicksort, I can." Same code, different signal.

**Gotcha 2 — Python-specific built-ins that don't exist in C/Java.** If the interviewer is cross-language fluent, using `Counter` on LC 347 is fine but be ready to describe "in Java I'd use a HashMap with manual increment."

**Gotcha 3 — the "obvious" built-in is sometimes the wrong one.** For LC 215 Kth Largest, `sorted(arr)[-k]` is O(n log n); `heapq.nlargest(k, arr)` is O(n log k); Quickselect is O(n) average. Picking the right built-in is part of the signal.

**Gotcha 4 — never leave built-in use unexplained.** "I'm using `Counter` because..." is mid-tier; "Counter gives me O(n) frequency construction and O(1) equality, vs sorting at O(n log n)" is senior-tier.
