# Duplicate Keys, Join Multiplication, and the Meaning of a Row

You start with four laboratory readings, add a station lookup, and end up with six rows. Where did the extra two come from? A join can enrich a dataset while quietly multiplying its records, especially when join keys repeat. Those repeated keys might describe several real observations in one category, an accidentally copied record, or a key that leaves out part of an entity's identity. The spreadsheet may look tidy in all three cases. The meaning of its rows is what tells us how to proceed.

Section 10.1.5, “Duplicated Keys,” of *Data Science Fundamentals with R, Python, and Open Data* uses two linked tables to explain this distinction. Its central lesson is that joins preserve rows according to matching relationships, not according to a researcher’s unstated idea of what a row ought to represent. This article develops that lesson through join cardinality, a worked example, and a practical validation workflow.

## Repeated keys are not automatically duplicate records

Suppose a transaction table has one row per purchase and a customer table has one row per customer. The customer identifier should usually be unique in the customer table, but it is expected to recur in the transaction table: one customer may make many purchases. Repetition in a key column is therefore not enough to conclude that rows are erroneous.

It helps to distinguish three things:

- A **repeated key** is a value that occurs in multiple rows of a selected key column.
- A **duplicate row** is a row whose values match another row across the columns being compared.
- A **distinct observation** is a real-world unit that the data is intended to represent, such as a separate purchase.

These categories overlap but are not interchangeable. Two purchases by one customer have a repeated customer key and may differ in product or time. Two separately recorded purchases can even have identical values if the table lacks a transaction identifier. Conversely, two rows with different values might still refer to the same underlying event if a correction or data-entry variation was not reconciled.

The source section first demonstrates repeated records on the left side of a join. Some are similar but differ in purchase details; one pair is identical. Joining each purchase to a city-to-country lookup preserves those six left-side records when each city has a single lookup match. That is the intended behavior: enriching a transaction does not itself require transactions to be unique.

## Where the extra rows come from

A join matches rows by key. If a left-side key appears $m$ times and its matching right-side key appears $n$ times, the matched key contributes $m \times n$ output rows in a standard relational join. Each left row is paired with every right row that has the same key.

Try a small count: one purchase row has a city key that matches three lookup records, so the join produces three versions of that purchase row. Now give the left table two rows for that city. With three matches on the right, the key group produces $2 \times 3 = 6$ joined rows. The join is doing exactly what its matching rule asks.

A left join also retains left rows that have no right-side match, typically with missing values in the right-side columns. An inner join drops unmatched left rows. When every left-side key has exactly one right-side match, these join types return the same left-row count and matched values. The source’s initial example has that property. Once keys are duplicated or missing, the distinction matters.

The source then duplicates every row in its lookup table and joins twice: once to attach a buyer’s country and once to attach a seller’s country. Each lookup contributes two matches per city. Consequently, each original purchase row is represented $2 \times 2 = 4$ times after both joins. Starting with six purchases gives $6 \times 4 = 24$ output rows. This arithmetic follows from the two matching choices at each join; it is not evidence of 24 purchases.

More generally, for a chain of joins where each left row has a fixed number of matches $r_i$ at join $i$, its output multiplicity is the product of the match counts, $\prod_i r_i$. Real datasets can have different match counts per key, so inspect the key-level counts rather than assuming one constant multiplier.

## Worked example: enriching laboratory readings

Imagine a readings table with four logically distinct observations. Each row records an observation ID, a station code, and a measurement:

| Observation ID | Station | Measurement |
|---|---|---:|
| R101 | S7 | 12.0 |
| R102 | S7 | 12.4 |
| R103 | S9 | 10.8 |
| R104 | S9 | 11.1 |

A station lookup is meant to add each station’s region:

| Station | Region |
|---|---|
| S7 | North |
| S7 | North |
| S9 | South |

The repeated S7 row might be a duplicated lookup record, or it might reflect a real distinction omitted from the displayed columns. Joining without investigating yields two matches for each S7 reading and one for each S9 reading. The expected output count is $2 \times 2 + 2 \times 1 = 6$ rows: R101 and R102 each occur twice; R103 and R104 each occur once.

Check the arithmetic another way: there are two S7 observations, each with two matches, contributing four rows. There are two S9 observations, each with one match, contributing two. Thus $4 + 2 = 6$. The readings table still contains four observations. The extra two joined rows arise from the lookup’s repeated key.

Before treating those extra rows as errors, determine the lookup’s intended grain. If it should contain one row per station and the two S7 records are accidental copies, deduplicating the lookup on its complete set of relevant fields may be appropriate. If those rows represent different station periods, instruments, or regions, the key is incomplete. Add the missing matching condition, such as a valid date range, or resolve the ambiguity according to the data model. Simply keeping an arbitrary first match would hide the problem.

The observation ID is also important. It distinguishes R101 from R102 even though their station and measurements could, in some dataset, happen to be identical. A stable identifier makes it possible to verify that the four original observations remain represented after enrichment.

## Why removing identical output rows can be wrong

The source section highlights a subtle failure: after the duplicated lookup has multiplied rows, applying a whole-row duplicate-removal operation seems to restore a tidy result. But identical-looking purchases are not necessarily the same purchase. If two real events have identical values in every recorded field, deduplicating on all those fields collapses them into one and changes the record count.

Functions such as R’s `duplicated()` and `distinct()` operate on values in the data supplied to them. They do not know the real-world identity of an observation. A uniqueness check over all columns asks whether the recorded rows are value-identical; it does not answer whether they represent the same event. Selecting only some columns for distinctness changes the question again, because rows differing in excluded columns may be merged.

The source proposes adding a unique row identifier to distinguish logically distinct left-side records. In a durable data pipeline, prefer an existing transaction or observation ID when available. If none exists, a generated row number can preserve records within a particular input ordering, but it is not necessarily stable when files are reordered or regenerated. An identifier should encode identity reliably, not merely make a uniqueness test pass.

## A reproducible join audit

Before joining, pause for a useful question: what does one row represent? Write down the intended grain of each table and the columns that identify it. Then give the lookup key a quick audit. Count its occurrences and list keys with more than one match. Check for missing keys, whitespace or capitalization inconsistencies, and differences in types or units. A few small checks now can save a much longer investigation later.

After joining, validate both row counts and identities. For a many-to-one enrichment, the output should normally contain one row per input observation; if it does not, investigate before continuing. Compare the set and count of observation IDs before and after. Check unmatched-key counts as well as multiply matched keys. Keep a small diagnostic summary in the analysis output so another researcher can reproduce the decision.

Do not assume that a join is one-to-one because the column names sound like identifiers. Verify the data. Likewise, do not “fix” multiplication by deleting rows until you know whether the multiplicity came from accidental duplication, a legitimate one-to-many relationship, or an incomplete key.

## Applications and assumptions

These principles apply to merging clinical measurements with patient metadata, adding geographic classifications to survey responses, connecting products to categories, or attaching calibration information to instrument readings. In each case, the intended relationship determines the expected cardinality. A patient may have many measurements, while a patient metadata table may be intended to have one current record or several historical records keyed by time.

The arithmetic above assumes ordinary equality matching on the stated key and that each matching right row contributes an output row. Specific libraries can differ in their handling of null keys, relationship warnings, or ordering. Check the documentation for the tool you use, and test a minimal example when behavior matters. The general diagnostic remains: count matches per key, then reason about how those counts affect each input row.

## Common pitfalls

1. **Treating every repeated key as corruption.** Repeated keys are expected on the “many” side of a relationship.
2. **Assuming a join preserves row count.** It does for some many-to-one left joins, not for arbitrary many-to-many matches.
3. **Deduplicating the joined result blindly.** This can erase distinct but value-identical observations.
4. **Using a non-unique identifier as though it were unique.** Names, dates, and categories often identify groups rather than individual records.
5. **Ignoring unmatched rows.** A row-count check alone can miss lost matches in an inner join or missing lookup values in a left join.

## Exercises with short answers

**1.** One left row matches four right rows. How many joined rows does that left row produce?  
**Answer:** Four, under ordinary join matching.

**2.** There are three left rows with key K and two right rows with key K. How many output rows come from K?  
**Answer:** $3 \times 2 = 6$.

**3.** Two records match on every recorded value. Can you conclude they represent the same event?  
**Answer:** No. You need an identity rule or identifier that reflects the data’s intended grain.

**4.** A lookup key has repeated values, but each row refers to a different time period. What should you investigate?  
**Answer:** Whether time belongs in the join condition, or whether the records need another documented resolution rule.

## The practical rule

Keep two questions close at hand: how many matches does each key have, and what does each row represent? Verify uniqueness where you expect it, calculate the match counts, preserve observation identifiers, and investigate before removing rows. Once those checks are routine, four readings becoming six rows is a puzzle you can explain. The same habit helps you catch a silent row explosion or a destructive deduplication before it changes your analysis.

---

Published 2026-10-02.

**Source:** Data Science Fundamentals with R, Python, and Open Data -- Marco Cremonini, section **10.1.5 Duplicated Keys**.

This is an original explanatory note; the source book is not redistributed.
