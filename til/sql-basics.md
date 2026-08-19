# SQL — running notes

One file, added to as SQL 50 goes on. Newest section at the bottom.

---

## Select — the shape of every query

```sql
SELECT  name, referee_id     -- which COLUMNS come back
FROM    Customer             -- which TABLE they come from
WHERE   referee_id != 2      -- which ROWS survive
```

Three questions, always in this order: which columns, from where, which rows.
`SELECT *` means every column.

`WHERE` runs once per row. It looks at that row, decides true or false, and
keeps the row only if the answer is true.

## Comparison operators

| Operator | Means |
|---|---|
| `=` | equals — one `=`, not `==` |
| `!=` or `<>` | not equal |
| `<` `>` `<=` `>=` | the usual |
| `AND` | both conditions must hold |
| `OR` | either one is enough |

## NULL — the one that catches everyone

NULL is not a value. It means *unknown*. Nothing is equal to unknown, and
nothing is unequal to it either.

So `referee_id != 2` on a NULL row is neither true nor false — it is UNKNOWN.
And `WHERE` keeps only rows that are TRUE, so UNKNOWN gets thrown away exactly
like FALSE does. The NULL rows silently vanish.

The only way to test for it:

```sql
WHERE referee_id IS NULL        -- has no value
WHERE referee_id IS NOT NULL    -- has some value
```

Never `= NULL`. It compiles and it always matches nothing.

**The habit worth building:** any time you write `!=` or `<>` on a column that
is allowed to be empty, ask "what happens to the NULL rows?" — and if you want
them, say so:

```sql
WHERE referee_id != 2 OR referee_id IS NULL
```

*Where it came up:* SQL 50 #2, Find Customer Referee, 2026-08-18.

## The question to ask of any result

**One row of this result is one what?**

That is the grain. Get it wrong and every number is wrong — you are counting
the wrong thing. It is the same question as the pandas one: after a `GROUP BY`,
one row is one distinct value of the column you grouped on.
