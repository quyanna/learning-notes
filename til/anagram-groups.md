# TIL: a dict can't be a key, a tuple can

**Date:** 2026-08-20 | **Where it came up:** Blind 75 problem 4, Group Anagrams
**Pattern:** Arrays & Hashing | **Complexity:** O(total letters) time / O(number of distinct keys) space

## The thing
Anagrams are the same letters in a different order, so the letter counts are
identical. That makes a letter count the natural group key: build it for each
word, and words that share a key belong together.

Python won't let you use the count directly if you store it in a dict, because
dict keys must be immutable and a dict isn't. A tuple is. `tuple(some_list)`
freezes a list into one, and that's the whole conversion.

`ord(letter) - ord("a")` turns a character into its position 0 to 25, which is
how a 26 slot list gets indexed without a lookup table.

The empty string needs no special case. The counting loop runs zero times, so it
produces 26 zeros, which is a perfectly good key that all empty strings share.

Sorting each word would also work and is one line shorter, but sorting a word of
length k costs k log k where counting it costs k. Counting is the cheaper one and
it is the one that holds up as words get longer.

## The command / snippet that proves it
```python
counts = {"a": 2, "b": 1}
d = {}
d[counts] = 1                  # TypeError: unhashable type: 'dict'
d[tuple(counts.items())] = 1   # fine

slots = [0] * 5
slots[2] += 1                  # [0, 0, 1, 0, 0]
tuple(slots)                   # (0, 0, 1, 0, 0), now usable as a key

ord("c") - ord("a")            # 2
```

## Also today (SQL): GROUP BY is what dedupes, not HAVING
`SELECT author_id AS id FROM Views GROUP BY author_id, viewer_id HAVING author_id = viewer_id`
returns one row per author who viewed their own article, with no `DISTINCT`
anywhere. Grouping on the pair collapses every repeat of that pair into a single
row, and that collapse is the deduplication. `HAVING` runs afterwards and only
throws away the groups where the two columns differ. `WHERE` couldn't have done
that job, because `WHERE` filters rows before the groups exist.

## Also today (pandas, bonus): none of the verbs mutate
```python
df[df["a"] == df["b"]]           # WHERE, a boolean mask compared row by row
df.drop_duplicates()             # DISTINCT
df.rename(columns={"a": "id"})   # AS
df.sort_values("id")             # ORDER BY
```
Every one returns a new frame and leaves the original untouched, which is why
chaining works: each verb hands its result to the next. `df["a"]` gives a Series,
`df[["a"]]` gives a DataFrame, and that difference is what the judge checks. The
only thing that mutates is assignment, `df["new"] = ...`.

## Why it matters later
The tuple key is the reusable move: any time you need to group things by a
computed property, the property has to be hashable first. And "GROUP BY dedupes,
HAVING filters the groups" is the distinction that stops mattering theoretically
and starts mattering practically in the Basic Joins section.
