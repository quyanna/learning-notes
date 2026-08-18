# TIL — Counting into a Python dict

**Date:** 2026-08-18 | **Where it came up:** Blind 75 problem 2, Valid Anagram
**Pattern:** Arrays & Hashing | **Complexity:** O(n + m) time / O(1) space (at most 26 distinct characters)

## The thing
A dict is the JS object, with one difference that matters: reading a missing key
raises `KeyError` instead of giving `undefined`. `.get(key, default)` is the
safe read, and `counts.get(x, 0) + 1` is the whole counting idiom — "whatever
this key's count is, or 0 if I've never seen it, plus one".

Dicts also compare by content: `{"a": 1} == {"a": 1}` is `True` in Python,
where `===` on two JS objects is `false`.

One dict is enough for two collections: count up while walking the first, count
down while walking the second, then every value should be 0.

## The command / snippet that proves it
```python
socks = ["red", "blue", "red"]

counts = {}
for colour in socks:              # iterates values, like JS for...of
    counts[colour] = counts.get(colour, 0) + 1
# {"red": 2, "blue": 1}

counts.get("green")               # None - no crash
counts.get("green", 0)            # 0
counts.values()                   # the counts, for a final sweep
```

## Also today (SQL) — why `!= 2` loses the NULL rows
A comparison against NULL evaluates to UNKNOWN, not TRUE or FALSE, and `WHERE`
keeps only rows that are TRUE — so UNKNOWN is dropped exactly like FALSE.
`IS NULL` is the only operator that tests for it.

```sql
WHERE referee_id != 2 OR referee_id IS NULL
```

## Why it matters later
Every later Arrays & Hashing problem is a dict or a set doing the same
space-for-time trade, and the NULL rule bites in every real report where an
outer join left blanks behind.
