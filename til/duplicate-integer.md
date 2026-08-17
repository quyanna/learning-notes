# TIL — Python sets, coming from JavaScript

**Date:** 2026-08-17 | **Where it came up:** Blind 75 problem 1, Contains Duplicate
**Pattern:** Arrays & Hashing | **Complexity:** O(n) time / O(n) space

## The thing
The Arrays & Hashing trade is: spend O(n) space to buy O(n) time. Brute force
compares every pair, O(n²). Sorting first gets O(n log n) but costs O(n) space
depending on the sort. A hash set gets O(n) time because membership is a hash
lookup rather than a scan.

Coming from JS: `new Set()` is `set()`, `s.has(x)` is `x in s`, `x.length` is
`len(x)`, `true` is `True`, and `{}` is an empty dict — not an empty set.
Indentation is the block delimiter; `def`/`for`/`if` lines end in `:`.

LeetCode's `class Solution:` and `self` are their grader's harness, not Python
style — they build one `Solution` object and call the method on it. Outside
LeetCode you would just write a plain function.

## The command / snippet that proves it
```python
s = set(items)     # build from any list
s = set()          # empty set - NOT {}
s.add(x)
x in s             # membership, fast regardless of size
len(s)
```

## Why it matters later
Every later Arrays & Hashing problem is this same space-for-time trade, and
Python is the language for pandas, the Zoomcamp and the agentic course.
