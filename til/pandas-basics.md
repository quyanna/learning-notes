# pandas — running notes

One file, added to as the Kaggle course and the projects go on. Newest section
at the bottom.

The mental model: a **DataFrame** is a table. A **Series** is one column of it.
Almost every method returns a *new* object rather than changing the old one, so
you have to assign the result to something or it is thrown away.

---

## Lesson 1 — creating, reading, writing

```python
import pandas as pd

df = pd.read_csv("file.csv")        # the way you get real data
df.to_csv("out.csv", index=False)   # index=False stops it writing the row numbers

df.head()      # first 5 rows - use this constantly
df.shape       # (rows, columns)
df.columns     # the column names
```

Building one by hand, mostly for toy examples:

```python
pd.DataFrame({"city": ["Calgary", "Edmonton"], "shows": [12, 8]})
pd.Series([12, 8], name="shows")     # a single column
```

## Lesson 2 — indexing and selecting

Getting one column gives you a Series:

```python
df["shows"]        # always works
df.shows           # same thing, breaks on names with spaces
df[["city", "shows"]]   # a LIST of names gives a DataFrame back
```

Then the two selectors. The difference is the whole lesson:

| | Selects by | Example |
|---|---|---|
| `.iloc` | **position** — where it sits, counting from 0 | `df.iloc[0]` first row |
| `.loc` | **label** — the index value or the column name | `df.loc[0, "city"]` |

```python
df.iloc[0]              # first row
df.iloc[:3]             # first 3 rows - stops BEFORE 3, like Python slicing
df.iloc[:, 0]           # every row, first column  (row , column)

df.loc[0, "city"]       # by label
df.loc[:, ["city", "shows"]]
df.loc[0:2]             # rows 0,1 AND 2 - .loc INCLUDES the end, .iloc does not
```

That last line is the gotcha worth memorising: `.iloc` stops before the end,
`.loc` includes it.

**Selecting rows by a condition** — the one used most:

```python
df[df.shows > 10]                          # rows where the condition is true
df[(df.shows > 10) & (df.city == "Calgary")]   # & is AND, | is OR
df[df.city.isin(["Calgary", "Edmonton"])]      # any of these
df[df.city.notna()]                            # not empty - pandas' IS NOT NULL
```

Each condition must be wrapped in its own `( )`, and it is `&` / `|`, not
`and` / `or`.

## Lesson 3 — summary functions and maps

**Summary functions** answer "what does this column look like":

```python
df.shows.describe()      # count, mean, min, max, quartiles - all at once
df.shows.mean()
df.city.unique()         # the distinct values
df.city.value_counts()   # the distinct values AND how many of each
```

`value_counts()` is the one you reach for most: it answers "how many rows per
category" in a single call.

**Maps** transform every value in a column and hand back a new column:

```python
mean = df.shows.mean()
df.shows.map(lambda s: s - mean)          # Series in, Series out

df.apply(lambda row: row.shows * 2, axis=1)   # whole ROWS, one at a time
```

`map` works on one column. `apply` with `axis=1` works on whole rows. Neither
changes the original — assign the result:

```python
df["shows_centred"] = df.shows.map(lambda s: s - mean)
```

The faster shortcut for simple arithmetic, no lambda needed:

```python
df.shows - mean          # pandas applies it to every value for you
```

## Lesson 4 — grouping and sorting

`groupby` splits the table into piles, then runs a summary on each pile.

```python
df.groupby("city").shows.sum()      # one row per city, shows added up
df.groupby("city").size()           # one row per city, how many rows in it
df.groupby("city").shows.agg(["min", "max", "count"])   # several at once
```

**The question to ask every time: one row of this result is one what?**
After `groupby("city")` the answer is: one city. Every number on that row is an
aggregate over the rows that fell into that pile. Get this wrong and every
number is wrong — the same grain question as SQL's `GROUP BY`.

Grouping by two columns gives one row per *combination*, and a two-level index:

```python
df.groupby(["city", "genre"]).size()
df.groupby(["city", "genre"]).size().reset_index(name="n")   # back to a flat table
```

`reset_index()` is what turns that stacked index back into ordinary columns —
worth doing before you plot or export anything.

**Sorting** is separate, and does not happen for free:

```python
df.sort_values(by="shows")                    # smallest first
df.sort_values(by="shows", ascending=False)   # largest first
df.sort_values(by=["city", "shows"])          # city, then shows within it
df.sort_index()                               # back to index order
```

`groupby` returns rows in index order, not in size order. If you want "top 5
cities by shows" you have to `sort_values(ascending=False).head(5)` yourself.

---

## The two habits worth keeping

1. **Assign the result.** Nearly nothing changes the DataFrame in place.
2. **Say the grain out loud** before trusting a number: one row is one *what*?
