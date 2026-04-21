# python-dsa-cheatsheet

Atomic, signal-only notes for Python DSA / LeetCode / coding interviews.

Every entry is:
- one concept, one file
- tied to specific LeetCode problem numbers, with **inline-commented code showing how the tool is used** (not just named)
- tied to named patterns
- Big-O impact called out when non-trivial
- ends with the gotcha that costs you the interview

No intros. No tutorials. No "what is X." Read what's useful, skip the rest.

---

## Index

### Language essentials
| Note | Triggers on | Patterns |
|---|---|---|
| [enumerate — index + value](notes/enumerate-index-and-value.md) | LC 1, 121, 287, 448 | hash_map, index_tracking |
| [Unpacking & swap](notes/unpacking-and-swap.md) | LC 24, 70, 88, 189, 198, 206, 344 | swap, rolling_state |
| [Slicing tricks](notes/slicing-tricks.md) | LC 125, 151, 189, 344, 1913 | reversal, slice_copy |
| [reversed() vs [::-1]](notes/reversed-vs-slicing.md) | LC 9, 42, 125, 151, 238 | reverse_iteration |
| [range backwards](notes/range-backwards.md) | LC 42, 84, 198, 238, 496 | reverse_iteration, monotonic_stack |
| [List comprehension patterns](notes/list-comprehension-patterns.md) | LC 1, 54, 62, 200, 448, 771 | filter, 2D_init, flatten |
| [Walrus `:=`](notes/walrus-operator.md) | LC 20, 234, 876 | linked_list, parse_loops |
| [Chained comparison + misc gotchas](notes/gotchas.md) | catch-all | correctness |

### Numerics
| Note | Triggers on | Patterns |
|---|---|---|
| [32-bit integer bounds](notes/32-bit-integer-bounds.md) | LC 7, 8, 29, 50 | overflow |
| [Integer division with negatives](notes/integer-division-with-negatives.md) | LC 7, 29, 166 | modulo, digit_extraction |
| [Overflow-safe arithmetic](notes/overflow-safe-arithmetic.md) | LC 7, 8, 29, 50 | overflow_detection |
| [float('inf') sentinels](notes/float-inf.md) | LC 64, 84, 121, 322, 743, 787 | dp_min, graph_dist |
| [divmod for grid indexing](notes/divmod-for-grid-indexing.md) | LC 54, 59, 66, 67, 168, 415 | matrix, carry |
| [ord/chr alphabet indexing](notes/ord-chr-alphabet-index.md) | LC 49, 208, 242, 383, 438, 567, 848 | anagram, trie |

### Stdlib weapons
| Note | Triggers on | Patterns |
|---|---|---|
| [defaultdict — no KeyError](notes/defaultdict-no-keyerror.md) | LC 49, 133, 207, 811 | graph_adjacency, grouping |
| [dict.get / setdefault](notes/dict-get-default.md) | LC 1, 49, 205, 219, 387 | hash_map, one_shot_default |
| [heapq has no max-heap](notes/heapq-no-max-heap.md) | LC 23, 215, 295, 347, 621, 703, 973 | top_k, merge_k, median_stream |
| [Counter.most_common(k)](notes/counter-most-common.md) | LC 49, 242, 347, 383, 451, 692 | frequency, top_k, anagram |
| [bisect — sorted insertion](notes/bisect-sorted-insertion.md) | LC 4, 34, 35, 300, 354, 704, 981 | binary_search, LIS |
| [@cache for memoization](notes/cache-decorator-for-dp.md) | LC 70, 72, 139, 198, 322, 1143 | dp_1d, dp_2d |
| [list.pop(0) is O(n) — use deque](notes/list-pop-zero-is-on.md) | LC 102, 127, 200, 994 | bfs |
| [2D list aliasing](notes/2d-list-aliasing.md) | LC 62, 63, 64, 72, 79, 221 | grid_dp, backtracking |
| [sum() with generator](notes/sum-with-generator.md) | LC 191, 202, 338, 771, 1313, 2011 | frequency, boolean_sum |
| [any() / all() short-circuit](notes/any-all-short-circuit.md) | LC 36, 79, 217, 766, 896 | boolean_reduction |
| [Set algebra & | − ^](notes/set-algebra.md) | LC 128, 202, 217, 349, 350, 771, 2215 | set_membership |
| [zip(*matrix) transpose](notes/zip-star-transpose.md) | LC 36, 48, 566, 832, 867 | matrix |
| [Multi-key sort (tuple key)](notes/multi-key-sort.md) | LC 56, 253, 692, 791, 937, 1636 | sorting |
| [cmp_to_key custom compare](notes/cmp-to-key-custom-compare.md) | LC 179, 937 | custom_sort |
| [OrderedDict for LRU](notes/ordered-dict-lru.md) | LC 146, 460, 1429 | cache, order_preservation |
| [itertools.accumulate](notes/itertools-accumulate.md) | LC 53, 303, 560, 724, 1480 | prefix_sum, running_reduction |
| [itertools.pairwise](notes/itertools-pairwise.md) | LC 164, 217, 674, 896 | adjacent_check |
| [itertools.combinations / permutations / product](notes/itertools-combinations-permutations.md) | LC 17, 46, 47, 77, 78 | enumeration |
| [String / number one-liners](notes/string-one-liners.md) | LC 14, 20, 28, 67, 125, 151, 191, 372, 1979 | parsing |

### Patterns (templates + anchors)
| Note | Triggers on | Complexity |
|---|---|---|
| [Two pointers](notes/two-pointers-template.md) | LC 11, 15, 26, 42, 125, 167, 283, 344 | O(n) |
| [Sliding window](notes/sliding-window-template.md) | LC 3, 76, 209, 239, 424, 438, 567 | O(n) |
| [Top-K combo (three ways)](notes/top-k-combo.md) | LC 215, 347, 451, 692, 703, 973, 1636 | O(n log k) |

### Meta
| Note | Use when |
|---|---|
| [Interview opener checklist](notes/interview-opener-checklist.md) | first 90 seconds of every round |
| [Gotchas that eat interviews](notes/gotchas.md) | when "works on small input but fails submission" |

---

## Note format

Every note follows this shape:

```markdown
# [Concept]

**TL;DR:** one line

**Patterns:** tag1, tag2

## Where it matters — with usage
### LC X Problem Name — one-line why
  ```python
  # minimal snippet with inline comment showing the tool in this context
  ```

**Gotcha 1:** the thing nobody tells you
**Gotcha 2:** ...
```

Scannable. Searchable by LC number. Every snippet is mid-interview-copyable.

## Contributing

Pull requests welcome if they:
- follow the atomic-note format above
- cite real LC problem numbers with **inline-commented code anchors**
- trim fluff — "less explanation, more signal" is a hard constraint
- fit on one screen (≤ ~40 lines of rendered markdown)

## License

MIT.
