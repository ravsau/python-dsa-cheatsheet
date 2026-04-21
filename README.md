# python-dsa-cheatsheet

Atomic, signal-only notes for Python DSA / LeetCode / coding interviews.

Every entry is:
- one concept, one file
- tied to specific LC problem numbers
- tied to named patterns
- Big-O impact called out when non-trivial
- minimal code, no commentary
- ends with the gotcha that costs you the interview

No intros. No tutorials. No "what is X." Read what's useful, skip the rest.

---

## Index

### Language & numerics
| Note | Triggers on | Patterns |
|---|---|---|
| [32-bit integer bounds](notes/32-bit-integer-bounds.md) | LC 7, 8, 29, 50 | overflow |
| [Integer division with negatives](notes/integer-division-with-negatives.md) | LC 7, 29, 166 | modulo, digit extraction |
| [Overflow-safe arithmetic](notes/overflow-safe-arithmetic.md) | LC 7, 8, 29, 50 | overflow detection |
| [2D list aliasing](notes/2d-list-aliasing.md) | LC 62, 63, 64, 72, 79, 221 | grid DP, backtracking |
| [list.pop(0) is O(n)](notes/list-pop-zero-is-on.md) | LC 102, 127, 200, 994 | BFS |
| [Mutable default args](notes/mutable-default-args.md) | any recursion with accumulator | backtracking |

### Stdlib weapons
| Note | Triggers on | Patterns |
|---|---|---|
| [heapq has no max-heap](notes/heapq-no-max-heap.md) | LC 23, 215, 295, 347, 973 | top_k, merge_k, median_stream |
| [Counter.most_common(k)](notes/counter-most-common.md) | LC 347, 451, 692 | frequency, top_k |
| [bisect for sorted insertion](notes/bisect-sorted-insertion.md) | LC 35, 300, 704, 4 | binary_search, LIS |
| [@cache for memoization](notes/cache-decorator-for-dp.md) | LC 70, 139, 198, 322, 746 | dp_1d, dp_2d |
| [divmod for grid indexing](notes/divmod-for-grid-indexing.md) | LC 54, 59, 289 | matrix |
| [ord/chr alphabet indexing](notes/ord-chr-alphabet-index.md) | LC 49, 242, 383, 567 | anagram, frequency |
| [deque for sliding window](notes/deque-for-sliding-window.md) | LC 239, 862 | monotonic_deque |

### Patterns (templates)
| Note | Triggers on | Complexity |
|---|---|---|
| [Two pointers](notes/two-pointers-template.md) | LC 11, 15, 42, 125, 344 | O(n) |
| [Sliding window](notes/sliding-window-template.md) | LC 3, 76, 209, 424, 567 | O(n) |
| [Binary search on answer](notes/binary-search-on-answer.md) | LC 410, 875, 1011, 1482 | O(n log range) |
| [Monotonic stack](notes/monotonic-stack.md) | LC 84, 496, 503, 739, 901 | O(n) |
| [Topological sort](notes/topological-sort.md) | LC 207, 210, 269, 310 | O(V+E) |
| [Union-Find](notes/union-find-template.md) | LC 200, 547, 684, 721, 952 | O(α(n)) |
| [Kadane's](notes/kadane.md) | LC 53, 152, 918 | O(n) |
| [Fast & slow pointers](notes/fast-slow-pointers.md) | LC 141, 142, 202, 287, 876 | O(n) |

### Problem-specific insight files
| LC | Problem | Why it has its own file |
|---|---|---|
| [007](problems/007-reverse-integer.md) | Reverse Integer | 32-bit overflow without 64-bit int |
| [146](problems/146-lru-cache.md) | LRU Cache | OrderedDict vs manual doubly-linked-list |
| [269](problems/269-alien-dictionary.md) | Alien Dictionary | topo sort with implicit graph construction |

---

## Contributing

Pull requests welcome if they:
- follow the atomic-note format above
- cite real LC problem numbers
- trim fluff — "less explanation, more signal" is a hard constraint
- fit on one screen (≤ ~40 lines of rendered markdown)

## License

MIT.
