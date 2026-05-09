# Interview narration playbook — what to say at each phase

**TL;DR:** Coding interviews are VERBAL. Silence loses points. Narrate every phase: opener → pattern → plan → trace → code → verify → complexity. The 7-phase script below is the difference between Hire and Strong Hire.

**Where it matters:** every coding interview round.

**Patterns:** verbal_signal, interview_skill

## The 7-phase narration framework

### Phase 1 — Opener (~30 sec)
Restate the problem to confirm understanding.

> *"Let me make sure I understand. We have [inputs] and need to return [output]. Is that right?"*
> *"What should happen when [edge case, e.g., empty input]?"*

### Phase 2 — Pattern identification (~1 min) — KEY
Name the algorithm class explicitly.

> *"This is [BFS / DP / sliding window / DFS / heap / two-pointer] because [signal]."*

| Signal | Pattern |
|---|---|
| "Minimum steps / shortest path / spreading" | BFS |
| "Number of ways / count / max value with overlap" | DP |
| "Longest substring with constraint" | Sliding window |
| "Tree, no shortest path" | DFS |
| "Kth largest / top-K" | Heap |
| "Pair finding in sorted array" | Two-pointer |
| "Intervals / pairs / [start,end]" | Sort + sweep |

### Phase 3 — Approach plan (~1-2 min)
> *"My plan is: [step 1], then [step 2], then [step 3]. I'll use [data structure] because [reason]. Time should be O(X)."*

### Phase 4 — Trace example (~2 min)
Walk example 1 with your plan BEFORE coding.

> *"Let me trace example 1. Starting at [state]. After step 1: [state]. ..."*

### Phase 5 — Coding narration (~10-15 min)
Don't type silently.

> *"First, edge case for [X]."*
> *"Now I initialize [Y]."*
> *"In the loop, I [action]. The key insight is [Z]."*

When stuck: *"Let me think... if I [option A] vs [option B]..."*

### Phase 6 — Test/trace verification (~2-3 min)
> *"Let me trace my code on example 1. After iteration 1: [state]. ..."*
> *"Final return: [value]. ✓ Matches expected."*

### Phase 7 — Complexity (~1 min)
> *"Time is O(N) because [reason]. Space is O(N) for [structure]."*

## When stuck — don't freeze

| Move | Phrase |
|---|---|
| Think aloud | *"Let me think out loud for a second..."* |
| Trace small | *"Let me try a small example..."* |
| State brute force first | *"Brute force would be O(N²) by [X]. Can we do better?"* |
| Ask for hint | *"I'm thinking [approach]. Am I on track?"* |

## Phrases NOT to use

```
❌ "Sorry, I'm bad at this..."
❌ "Umm..."
❌ "Let me just try something..." (no plan)
❌ Multiple apologies for mistakes

✅ "Let me think for a second..."
✅ "Let me trace an example..."
✅ "I'll start with [pattern], then refine..."
✅ "I had a bug — let me fix it." (matter-of-fact)
```

## Pattern-specific phrases

### BFS
> *"BFS because minimum steps. Deque for O(1) popleft. Each level = one [step/minute]. `size = len(q)` snapshots the level so I process exactly that many."*

### DP
> *"DP because overlapping subproblems. dp[i] means [X]. At index i, last decision was [option A] or [option B]. So dp[i] = max/min/sum."*

### Tree DFS
> *"Tree DFS = recursion. Three pieces: base case, do at this node, recurse on children. Returns [type] because [reason]."*

### Heap top-K
> *"Min-heap of size k. The kth largest is heap[0] because smallest of k largest = kth largest overall. Time O(N log K)."*

### Sliding window
> *"Variable sliding window. Three beats: expand right, shrink while [condition], record. For longest = shrink while invalid. For shortest = shrink while valid."*

### Intervals
> *"Sort by start. Walk through; if current overlaps with last, extend. Else append. O(N log N) dominated by sort."*

## Closing the interview

> *"To summarize: my approach was [X]. Time O(Y), space O(Z). Edge cases handled: [list]. Any concerns or follow-ups?"*

## Practice routine — 30 min before any interview

1. Pick a problem from your solved list
2. Solve it from scratch WHILE TALKING ALOUD (record yourself)
3. Listen back — count filler words, gaps of silence
4. Repeat with 2 more problems
5. Notice which phases felt awkward — practice those phrases more

After 30 min of aloud rehearsal, real-interview narration is 5x cleaner.

## TL;DR

> **The 7 phases: opener → pattern → plan → trace → code → verify → complexity. Memorize 1-2 phrases per phase. Talk through every step. Silence loses points; narration earns them.**
