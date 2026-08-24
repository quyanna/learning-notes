# TIL — Sorting a dict by its values

**Date:** 2026-08-24 | **Where it came up:** Blind 75 problem 5, Top K Frequent Elements
**Pattern:** Arrays & Hashing | **Complexity:** O(n log n) time / O(n) space

## The thing
A dict has no order you can use, and you never try to give it one. You take the
pairs out into a list and order the list. `sorted()` never changes what you hand
it; it returns a new list.

Iterating a dict gives keys only, so `sorted(counts)` sorts the numbers
alphabetically and ignores the counts entirely. `.items()` is what puts the value
next to the key, and that is the only reason `item[1]` inside the lambda has a
count to look at.

`Counter(nums)` from `collections` builds the whole frequency dict in one line,
replacing the `counts.get(x, 0) + 1` loop from Valid Anagram.

## The command / snippet that proves it
```python
from collections import Counter
socks = Counter(["red", "blue", "red", "red", "green", "blue"])
# Counter({'red': 3, 'blue': 2, 'green': 1})

list(socks)                  # ['red', 'blue', 'green']   keys only, counts invisible
sorted(socks)                # ['blue', 'green', 'red']   alphabetical, not by count
list(socks.items())          # [('red', 3), ('blue', 2), ('green', 1)]

sorted(socks.items(), key=lambda item: item[1], reverse=True)
# [('red', 3), ('blue', 2), ('green', 1)]   a NEW list, socks untouched
```

`key=` takes a function that says what to sort on. `item[1]` is the count half of
each pair, `reverse=True` puts the biggest first.

## Also today (SQL) — LENGTH()
`LENGTH(content)` gives the number of characters in a text column, usable
anywhere an expression is allowed, including `WHERE`.

```sql
SELECT tweet_id
FROM   Tweets
WHERE  LENGTH(content) > 15
```

No aggregation, so the grain is unchanged from the base table: one row per tweet.

## Why it matters later
The article lists two faster approaches for this problem, a min-heap at
O(n log k) and bucket sort at O(n), neither read yet. Sorting pairs by value is
also the shape of every "top N by count" question in pandas and SQL.
