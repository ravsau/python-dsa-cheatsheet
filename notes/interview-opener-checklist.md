# Interview opener — the first 90 seconds

**TL;DR:** Google (and most top-tier) grade the first 90 seconds heavily. Don't code. Clarify, constrain, state approach, name the data structure. Then code.

## The 5-step opener

### 1. Restate the problem
Read it aloud in your own words. Confirms comprehension. Surfaces ambiguity before you build on a wrong assumption.

> "So I'm given an array of integers and a target, and I need to return the **indices** of the two numbers that sum to target — not the numbers themselves."

### 2. Clarify inputs / constraints
Ask these every time (even if stated — showing the discipline matters):
- **Size range?** (n = 10 vs 10^5 vs 10^9 changes the algorithm)
- **Value range?** (negatives? overflow risk? sorted?)
- **Duplicates allowed?**
- **Can input be empty? single element?**
- **Is there always a solution? unique? first match or all?**
- **Can I modify the input in place, or should I preserve it?**

### 3. Talk about edge cases BEFORE coding
Out loud:
- "Empty input → return `[]`"
- "Single element → no pair, return `[]`"
- "Duplicates where same number is used twice → check index inequality"
- "Overflow if sum exceeds INT_MAX"

Writing edge cases later looks like you forgot. Writing them first looks deliberate.

### 4. Propose approach — cheapest first, then best
```
"Brute force is O(n²) with two nested loops.
Can we do better? If I iterate once and for each num check if target-num was already seen, that's O(n) with a hash map.
I'll go with the hash-map approach, trading O(n) space for O(n) time."
```

Get **explicit buy-in** — "Sounds good?" — before writing code. If they want brute force first, you'd rather know now than after 10 lines.

### 5. Name the data structure / pattern out loud
```
"I'll use a dict mapping num → index for O(1) complement lookup. This is the classic hash-map-complement pattern."
```

Saying the pattern name out loud is a **senior signal**. "I'll use a dict" is ok; "I'll use a dict because this is hash-map-complement on a pair-sum problem" is cracked.

---

## After coding — the last 60 seconds

### 1. Trace a small example by hand
```
nums = [2,7,11,15], target = 9
i=0: num=2, seen={}, look for 7, not there, seen = {2:0}
i=1: num=7, look for 2, found at index 0, return [0, 1] ✓
```

Test **with the actual input from the problem statement.** Not a made-up example.

### 2. State complexity explicitly
"Time O(n) — single pass. Space O(n) — dict stores up to n entries."

Don't wait for them to ask. It's part of the answer.

### 3. Mention edge-case tests
"Empty input returns []. No valid pair returns []. Duplicates (e.g. [3,3], target 6) handled because we check `target-num in seen` before inserting."

### 4. Prep for follow-ups
Common Google follow-ups — have a one-liner ready for each:
- "What if the array is **streamed** (too big to fit in memory)?"
  → Depends on constraint — if we just need to know IF a pair exists, maintain a rolling set.
- "What if it's **sorted**?"
  → Two pointers, O(n) time, O(1) space — no hash needed.
- "What if there are **k values that sum to target**?"
  → Sort + fix outer index + two pointers, or recursive k-Sum — O(n^(k-1)).
- "What if we want **all pairs**, not just one?"
  → Store lists of indices in the dict instead of single index.

---

## The anti-patterns (don't do these)

1. **Starting to code immediately.** You'll rewrite within 2 minutes. Save the time upfront.
2. **Mumbling through the approach.** Speak in complete sentences. The interviewer is also writing signals into a doc — clear speech = clear signals.
3. **Pretending not to need clarification.** Asking questions is a skill; skipping them is a weakness. The interviewer has a list of clarifications they expect you to ask.
4. **Assuming the "obvious" approach.** "Obviously we use a hash map" — why? Justify. Compare to alternatives. Even if brute force is dumb, name it first so you can dismiss it explicitly.
5. **Not checking complexity.** If asked "can you do better?", you should already have the answer — because you compared approaches before coding.

---

## Muscle memory drill

Run this checklist on every single problem for 3 days. By Day 4 it's automatic and you'll never skip it under pressure.

```
□ Restated in my own words
□ Asked about size / value range / duplicates / empty input
□ Listed 3+ edge cases
□ Proposed brute force → optimized
□ Got explicit buy-in
□ Named the pattern + data structure
□ Coded cleanly
□ Traced with the problem's example
□ Stated complexity
□ Listed one anticipated follow-up
```

Same muscle memory as syntax. Same payoff — except this one **separates mid-level from senior** in the first minute.
