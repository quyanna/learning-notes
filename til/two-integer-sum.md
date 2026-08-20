# TIL — The complement dict, and storing after the check

**Date:** 2026-08-19 | **Where it came up:** Blind 75 problem 3, Two Sum
**Pattern:** Arrays & Hashing | **Complexity:** O(n) time / O(n) space (dict holds at most n entries)

## The thing
The brute force compares every element against every other one — O(n²). The dict
turns the inner loop into a lookup: at each element, ask whether its *complement*
(`target - value`) has already been seen. That's O(1) per step, so one pass does it.

`enumerate(nums)` is how Python gives you index and value together — the
equivalent of JS `for (const [i, v] of arr.entries())`. Plain `for x in nums`
gives values only, and `range(len(nums))` gives indices only.

The line order is load-bearing. Check first, *then* store. If you store before
checking, an element becomes its own complement whenever `target == 2 * value`,
and you return `[i, i]` — two indices that are the same index. `[3, 3]` with
target 6 is the case that catches it.

## The command / snippet that proves it
```python
socks = ["red", "blue", "green"]

for index, colour in enumerate(socks):
    print(index, colour)          # 0 red / 1 blue / 2 green

# the complement idiom, on unrelated data
seen = {}
for i, price in enumerate([10, 40, 30]):
    if (50 - price) in seen:      # check first
        print(seen[50 - price], i)
    seen[price] = i               # store second
```

## Also today (SQL) — WHERE is a row filter, so the grain survives
A row satisfying two `OR` branches still appears once. `WHERE` tests each source
row and keeps or drops it; it can never duplicate one. That guarantee is exactly
what a `JOIN` gives up — a joined row can match many rows on the other side and
multiply.

```sql
SELECT name, population, area
FROM World
WHERE area >= 3000000 OR population >= 25000000
```

## Why it matters later
Every remaining Arrays & Hashing problem is this same space-for-time trade with a
different question asked of the dict. And "one row per what?" is the first thing
to ask of any query — it is about to stop being obvious in the Basic Joins section.
