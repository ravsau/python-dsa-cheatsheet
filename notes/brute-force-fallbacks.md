# Brute-force fallbacks — when stuck, start here

**TL;DR:** Every problem has a brute-force solution. In interview, ALWAYS state it first, then propose the optimal. Brute force often: try all possibilities, nested loops. Then optimize via the right pattern.

**Where it matters:** every coding interview. Critical when you can't immediately identify the optimal pattern.

**Patterns:** brute_force, interview_skill

## The interview script for brute-force-first

> *"Let me start with brute force. We could [O(n²) approach]. That works but is slow. Can we do better? Yes — [pattern] gives us O(n) because [reason]. Let me implement the optimized version."*

This shows three things:
1. You can solve it (brute force works)
2. You know what optimal looks like (you've identified the pattern)
3. You can articulate the trade-off

Even if you only manage brute force, you've shown problem-solving. Hire signal.

---

## Pattern-by-pattern: brute force → optimal

### Hash map problems

| Problem | Brute force | Optimal | Why optimal |
|---|---|---|---|
| LC 1 Two Sum | nested loops O(n²) for each pair | hash map O(n) | trade space for time |
| LC 49 Group Anagrams | for each str, compare to all others O(n²·k) | sorted-key dict O(n·k log k) | group by canonical key |
| LC 217 Contains Duplicate | nested loops O(n²) | set membership O(n) | constant lookup |

**Verbal:** *"Brute force: check every pair, O(n²). Better: hash map for O(1) complement lookup, O(n) total."*

### Two-pointer / sliding window

| Problem | Brute force | Optimal | Why |
|---|---|---|---|
| LC 3 Longest Substring | for each start, find longest valid O(n³) | sliding window O(n) | each element in/out once |
| LC 209 Min Subarray Sum | for each (i,j), compute sum O(n²) or O(n³) | sliding window O(n) | running sum + shrink |
| LC 15 3Sum | three nested loops O(n³) | sort + two-pointer O(n²) | reduce one dim |

**Verbal:** *"Brute force: nested loops O(n²) checking all subarrays. We can do O(n) with sliding window — each element enters and exits at most once."*

### Binary search

| Problem | Brute force | Optimal | Why |
|---|---|---|---|
| LC 704 Binary Search | linear scan O(n) | binary search O(log n) | halve search space each step |
| LC 33 Rotated Search | linear scan O(n) | modified binary search O(log n) | still halvable despite rotation |
| LC 875 Koko Eating | linear scan over hour rates O(n) | binary search on answer O(n log max) | answer is monotonic |

**Verbal:** *"Brute force: scan linearly, O(n). Since input is sorted, we can binary-search O(log n)."*

### Linked list

| Problem | Brute force | Optimal | Why |
|---|---|---|---|
| LC 206 Reverse LL | new list, traverse + prepend O(n) space | in-place pointer reversal O(1) | rewrite next pointers |
| LC 21 Merge Two | collect all values, sort O((n+m) log) | merge linearly O(n+m) | inputs already sorted |
| LC 141 Cycle | visited set O(n) space | fast/slow Floyd's O(1) | math trick |
| LC 19 Remove Nth | two-pass: count, then remove O(2n) | one-pass two-pointer O(n) | gap trick |

**Verbal:** *"Brute force: use a hash set for visited nodes, O(n) space. Floyd's tortoise-and-hare detects cycles in O(1) space using two pointers."*

### Trees

| Problem | Brute force | Optimal | Why |
|---|---|---|---|
| LC 104 Max Depth | DFS recursive | same — DFS is optimal | every node must be visited |
| LC 102 Level Order | DFS with depth tracking | BFS with deque | natural level-by-level |
| LC 226 Invert Tree | DFS recursive | same — DFS is optimal | every node visited |
| LC 543 Diameter | for each node, compute left depth + right depth O(n²) | DFS computing depth + diameter in one pass O(n) | combine computation |

**Note:** Most tree DFS problems have DFS as BOTH brute force AND optimal. Optimization is usually about doing in ONE pass rather than O(n) per node.

**Verbal:** *"DFS is the natural approach — visit each node once, O(n). Brute force would compute diameter per node separately, O(n²); the trick is to compute depth and diameter in the same DFS pass."*

### Graphs / grids

| Problem | Brute force | Optimal | Why |
|---|---|---|---|
| LC 200 Islands | DFS/BFS from each cell | DFS/BFS marking visited | already optimal O(R·C) |
| LC 994 Rotting Oranges | simulate minute by minute, scan whole grid each minute O(R·C·T) | BFS with queue O(R·C) | queue carries the frontier |
| LC 207 Course Schedule | DFS with cycle detection | topological sort (BFS or DFS) | linear in V+E |

**Verbal:** *"Brute force for 994: scan grid every minute, mark adjacent. O(R·C·T). BFS replaces the per-minute scan with a queue carrying the frontier — each cell processed once, O(R·C)."*

### Heap / top-K

| Problem | Brute force | Optimal | Why |
|---|---|---|---|
| LC 215 Kth Largest | full sort O(n log n) | quickselect O(n) avg, or heap O(n log k) | partial ordering |
| LC 703 Kth Largest Stream | for each query, sort O(n log n) | maintain heap of size k, O(log k) per add | running aggregation |
| LC 347 Top K Frequent | sort by frequency O(n log n) | heap of size k + Counter, O(n log k) | partial ordering + freq count |

**Verbal:** *"Brute force: sort the array, return arr[-k], O(n log n). Better: maintain a min-heap of size k as we scan, O(n log k). Or quickselect for O(n) average."*

### DP

| Problem | Brute force | Optimal | Why |
|---|---|---|---|
| LC 70 Climb Stairs | recursive Fibonacci O(2^n) | DP O(n) or O(1) | memoize overlapping subproblems |
| LC 198 House Robber | try all subsets O(2^n) | DP O(n) | reuse subproblems |
| LC 53 Max Subarray | try all subarrays O(n²) | Kadane DP O(n) | maintain running max |
| LC 322 Coin Change | recursive O(coins^amount) | DP O(amount·coins) | memoize |
| LC 139 Word Break | recursive try-all-splits O(2^n) | DP O(n²·m) | memoize |

**Verbal:** *"Brute force: recursive enumeration of all options, O(2^n). DP cuts this by reusing subproblem answers. Define dp[i] as [X], recurrence is [Y], O(n)."*

### Backtracking

| Problem | Brute force | Optimal | Why |
|---|---|---|---|
| LC 78 Subsets | backtracking IS optimal (output is 2^n) | same | can't be faster than output |
| LC 46 Permutations | backtracking IS optimal (output is n!) | same | same reason |
| LC 39 Combination Sum | backtracking | same | exponential output |

**Note:** When output size is exponential (2^n, n!), there's no "better than brute force." The brute force IS the algorithm.

**Verbal:** *"Output is 2^n subsets, so we need at least O(2^n) to enumerate them. Backtracking does exactly this — at each element, choose to include or skip, recurse, un-choose."*

### Intervals

| Problem | Brute force | Optimal | Why |
|---|---|---|---|
| LC 56 Merge Intervals | for each interval, check overlap with all O(n²) | sort + linear sweep O(n log n) | sort enables O(n) merge |
| LC 252 Meeting Rooms | nested loop checking overlaps O(n²) | sort + check adjacent O(n log n) | sorted = adjacent overlap only |
| LC 253 Meeting Rooms II | for each time, count active O(n²) | sort + heap O(n log n) | track end times |

**Verbal:** *"Brute force: check every pair O(n²). Sort first by start time, then linearly sweep merging adjacent overlaps. O(n log n) total — sort dominates."*

### OOP design

| Problem | Brute force | Optimal | Why |
|---|---|---|---|
| LC 155 Min Stack | scan whole stack on getMin O(n) | two parallel stacks O(1) | pre-pay during push |
| LC 146 LRU Cache | array + linear search O(n) | dict + DLL O(1) | combine DS |
| LC 380 Insert Del GetRandom | array (slow remove) or set (slow random) | array + dict swap trick O(1) | combine DS |

**Verbal:** *"Brute force: scan the stack for min on every getMin call, O(n). Better: maintain a parallel min-stack so getMin is O(1) at the cost of slightly heavier pushes."*

---

## When to start with brute force vs jump to optimal

### START with brute force when
- You don't immediately recognize the optimal pattern
- The problem looks unfamiliar
- You need 1-2 minutes to think
- You want to "warm up" your verbal narration

### JUMP to optimal when
- You recognize the pattern immediately (well-practiced)
- Brute force would be obviously bad (O(2^n) for small Mediums)
- The interviewer pushes "can you do better?" right away

### NEVER skip brute force when
- The interviewer asks "what's your approach?" (they often want to hear the brute force first)
- You're not 100% sure the optimal is correct (brute force is your safety net)

## The verbal pattern (memorize this)

> *"Let me think out loud. Brute force would be [approach] in O([X]) time. That's [too slow because Y / acceptable for small N].*
> 
> *Can we do better? I think [pattern] would work because [signal]. That gives us O([Z]).*
> 
> *Let me implement the optimized version."*

This 3-sentence template makes you sound senior. Always include the trade-off ("brute force is X, optimal is Y, the trick is Z").

## Brute-force CODE for the canonical problems

Below are actual brute-force implementations (not optimal). State and code these first in interviews if you can't find the optimal.

### LC 1 Two Sum — brute force O(n²)

```python
def twoSum(self, nums, target):
    for i in range(len(nums)):
        for j in range(i + 1, len(nums)):
            if nums[i] + nums[j] == target:
                return [i, j]
    return []
```

**Then optimize:** *"Better: hash map. Store each element's index, check if complement exists. O(n)."*

### LC 3 Longest Substring Without Repeating Characters — brute force O(n³)

```python
def lengthOfLongestSubstring(self, s):
    longest = 0
    for i in range(len(s)):
        for j in range(i, len(s)):
            sub = s[i:j+1]
            if len(set(sub)) == len(sub):    # check no dupes
                longest = max(longest, j - i + 1)
    return longest
```

**Then optimize:** *"Better: sliding window. Each char enters/exits once. O(n)."*

### LC 53 Max Subarray — brute force O(n²)

```python
def maxSubArray(self, nums):
    max_sum = float('-inf')
    for i in range(len(nums)):
        current = 0
        for j in range(i, len(nums)):
            current += nums[j]
            max_sum = max(max_sum, current)
    return max_sum
```

**Then optimize:** *"Better: Kadane's. dp[i] = max(nums[i], dp[i-1] + nums[i]). O(n)."*

### LC 70 Climbing Stairs — brute force O(2^n)

```python
def climbStairs(self, n):
    def helper(remaining):
        if remaining == 0:
            return 1
        if remaining < 0:
            return 0
        return helper(remaining - 1) + helper(remaining - 2)
    return helper(n)
```

**Then optimize:** *"Better: DP. dp[i] = dp[i-1] + dp[i-2]. O(n)."*

### LC 198 House Robber — brute force O(2^n) recursion

```python
def rob(self, nums):
    def helper(i):
        if i >= len(nums):
            return 0
        rob_this = nums[i] + helper(i + 2)
        skip_this = helper(i + 1)
        return max(rob_this, skip_this)
    return helper(0)
```

**Then optimize:** *"Better: DP. dp[i] = max(nums[i] + dp[i-2], dp[i-1]). O(n)."*

### LC 56 Merge Intervals — brute force O(n²)

```python
def merge(self, intervals):
    intervals.sort(key=lambda x: x[0])           # still need to sort, but the merge logic is brute below
    
    # Brute: repeatedly merge any two overlapping until none left
    changed = True
    while changed:
        changed = False
        result = []
        for interval in intervals:
            merged = False
            for i, r in enumerate(result):
                if interval[0] <= r[1] and interval[1] >= r[0]:    # overlap check
                    result[i] = [min(r[0], interval[0]), max(r[1], interval[1])]
                    merged = True
                    changed = True
                    break
            if not merged:
                result.append(interval)
        intervals = result
    return intervals
```

**Then optimize:** *"Better: sort by start, then linear sweep. If new interval overlaps last in result, extend; else append. O(n log n)."*

### LC 200 Number of Islands — DFS brute force IS the optimal

```python
def numIslands(self, grid):
    rows, cols = len(grid), len(grid[0])
    count = 0
    
    def dfs(r, c):
        if r < 0 or r >= rows or c < 0 or c >= cols or grid[r][c] != '1':
            return
        grid[r][c] = '0'
        dfs(r+1, c); dfs(r-1, c); dfs(r, c+1); dfs(r, c-1)
    
    for r in range(rows):
        for c in range(cols):
            if grid[r][c] == '1':
                count += 1
                dfs(r, c)
    return count
```

**Note:** for grid traversal, DFS already visits each cell once, so this IS optimal O(R·C). No "brute force" to mention as separate alternative.

### LC 215 Kth Largest in Array — brute force O(n log n)

```python
def findKthLargest(self, nums, k):
    return sorted(nums)[-k]    # sort all, take kth from end
```

**Then optimize:** *"Better: min-heap of size k. Each element O(log k). Total O(n log k). Or quickselect for O(n) average."*

### LC 21 Merge Two Sorted Lists — brute force O((n+m) log(n+m))

```python
def mergeTwoLists(self, list1, list2):
    # Brute: collect all values, sort, build new list
    values = []
    while list1:
        values.append(list1.val)
        list1 = list1.next
    while list2:
        values.append(list2.val)
        list2 = list2.next
    
    values.sort()
    
    dummy = ListNode()
    tail = dummy
    for v in values:
        tail.next = ListNode(v)
        tail = tail.next
    return dummy.next
```

**Then optimize:** *"Better: merge in-place. Inputs are already sorted. Walk both lists simultaneously, link smaller node first. O(n+m)."*

### LC 141 Linked List Cycle — brute force O(n) space

```python
def hasCycle(self, head):
    visited = set()
    while head:
        if head in visited:
            return True
        visited.add(head)
        head = head.next
    return False
```

**Then optimize:** *"Better: Floyd's tortoise-and-hare. Slow moves 1, fast moves 2. They meet iff cycle exists. O(1) space."*

### LC 226 Invert Binary Tree — DFS recursive IS optimal

```python
def invertTree(self, root):
    if not root:
        return None
    root.left, root.right = root.right, root.left
    self.invertTree(root.left)
    self.invertTree(root.right)
    return root
```

**Note:** this IS optimal — every node visited once. No simpler brute force.

### LC 104 Max Depth — DFS recursive IS optimal

```python
def maxDepth(self, root):
    if not root:
        return 0
    return 1 + max(self.maxDepth(root.left), self.maxDepth(root.right))
```

**Optimal.** No brute force to mention.

### LC 155 Min Stack — brute force O(n) for getMin

```python
class MinStack:
    def __init__(self):
        self.stack = []
    
    def push(self, val):
        self.stack.append(val)
    
    def pop(self):
        self.stack.pop()
    
    def top(self):
        return self.stack[-1]
    
    def getMin(self):
        return min(self.stack)         # ← BRUTE: scan whole stack every call, O(n)
```

**Then optimize:** *"Better: maintain a parallel min-stack. Each push tracks the running min. getMin reads min_stack[-1] in O(1)."*

### LC 146 LRU Cache — brute force using OrderedDict (almost optimal!)

```python
from collections import OrderedDict

class LRUCache:
    def __init__(self, capacity):
        self.cap = capacity
        self.cache = OrderedDict()
    
    def get(self, key):
        if key not in self.cache:
            return -1
        self.cache.move_to_end(key)
        return self.cache[key]
    
    def put(self, key, value):
        if key in self.cache:
            self.cache.move_to_end(key)
        self.cache[key] = value
        if len(self.cache) > self.cap:
            self.cache.popitem(last=False)
```

**Note:** OrderedDict IS O(1) per operation — this is actually the simplest acceptable solution. The "dict + DLL" version is what shows you understand the underlying data structure.

**Brute brute force** (scan to find LRU): O(n) per put when evicting. Avoid; OrderedDict or DLL is the right move.

### LC 994 Rotting Oranges — brute simulation O(R·C·T)

```python
def orangesRotting(self, grid):
    rows, cols = len(grid), len(grid[0])
    fresh = sum(row.count(1) for row in grid)
    time = 0
    
    while fresh > 0:
        changed = False
        new_rotten = []
        for r in range(rows):
            for c in range(cols):
                if grid[r][c] == 2:
                    for dr, dc in [(1,0),(-1,0),(0,1),(0,-1)]:
                        nr, nc = r+dr, c+dc
                        if 0 <= nr < rows and 0 <= nc < cols and grid[nr][nc] == 1:
                            new_rotten.append((nr, nc))
                            changed = True
        if not changed:
            return -1
        for r, c in new_rotten:
            grid[r][c] = 2
            fresh -= 1
        time += 1
    return time
```

**Brute force:** scan the whole grid each minute. O(R·C·T) where T = max minutes.

**Then optimize:** *"Better: BFS with multi-source queue. Each cell processed once. O(R·C)."*

### LC 78 Subsets — backtracking IS optimal

```python
def subsets(self, nums):
    result = []
    def backtrack(start, current):
        result.append(current[:])
        for i in range(start, len(nums)):
            current.append(nums[i])
            backtrack(i + 1, current)
            current.pop()
    backtrack(0, [])
    return result
```

**Note:** output is 2^n, so O(n·2^n) is optimal. No simpler brute force.

---

## TL;DR (one line)

> **For each problem, the brute force is usually "try everything" via nested loops or recursion. The optimal almost always uses a clever data structure (hash map, heap, deque) or insight (sort first, pointer movement, DP memoization). State brute force first in interviews — it's the safest starting move. For interview prep: practice both the brute force AND the optimal so you can verbalize the trade-off.**
