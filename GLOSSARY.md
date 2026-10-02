# Glossary

New concepts are appended as research notes are published.


<!-- publication:2026-10-02 -->

## 2026-10-02

Source note: [2026-10-02](articles/2026-10-02-duplicate-keys-join-multiplication.md).

### Join key

A column or set of columns used to identify corresponding records across tables. A key may repeat on one side when multiple observations belong to the same entity; uniqueness must be assessed according to the intended relationship, not inferred from the column’s label.

### Join cardinality

The relationship between the number of records on each side of a join for a key, commonly described as one-to-one, one-to-many, many-to-one, or many-to-many. Cardinality determines whether enrichment preserves row counts or multiplies them.

### Many-to-many join

A join in which at least one key value occurs multiple times on both sides. For a particular key with $m$ left rows and $n$ right rows, ordinary matching creates $m \times n$ pairs, which can substantially increase output rows.

### Duplicate row

A row whose recorded values match another row over the columns being compared. This is a statement about recorded values, not proof that the rows represent the same real-world event.

### Observation grain

The precise unit represented by one row, such as one purchase, one patient visit, or one station-day measurement. Stating the grain clarifies which repetitions are expected and what a valid unique identifier should distinguish.

### Surrogate identifier

A value assigned to identify a record when a natural, stable identifier is unavailable. It can preserve distinct records with identical measured fields, but a generated row number may depend on input order and should not be mistaken for a durable identity.

### Deduplication

Removing records judged redundant under an explicit rule, such as equality across selected columns or duplication of a known identifier. The rule determines what is considered redundant; deduplicating without understanding the data grain can erase valid observations.

### Unmatched key

A key value in one table for which no corresponding value exists in the other table. Unmatched keys can reflect incomplete coverage, inconsistent formatting, or genuine absence, and should be counted and interpreted rather than hidden.

### Finite numeric observation

A numeric value that is neither missing nor infinite nor NaN. Visualization and statistical routines often cannot use such values directly; handling them explicitly makes the analyzed sample size and omissions easier to report.
