# Editing Matrices Without Losing Track of Their Shape

A single deleted row can turn a matrix into a vector. A replacement vector can fill selected cells in an order that is easy to misread. These are small details, but they matter when a statistical analysis depends on exactly which observations and variables remain. R makes matrix selection and replacement concise; reliable work depends on understanding the indexing rules behind that concision.

This guide focuses on two operations: omitting matrix elements with negative indices, and overwriting selected elements with assignment. Both use square brackets, but selection and replacement follow different practical concerns. We will work through a new example, check the arithmetic, and connect the rules to reproducible data work.

## Negative indices omit; positive indices select

For a matrix `M`, the expression `M[rows, columns]` selects row and column positions. A negative index means omit that position. For example, `M[-2, ]` keeps every row except row 2, while `M[, -3]` keeps every column except column 3. You can omit several positions with a vector, as in `M[-c(1, 4), ]`.

The row and column positions are evaluated separately. Thus `M[-1, -2]` removes row 1 and column 2, whereas `M[-1, 3:2]` removes row 1 but selects columns 3 and 2 in that order. The latter is a useful reminder that positive indices can reorder results. Negative indices do not reorder what they retain: they exclude positions from the original ordering.

Do not mix positive and negative indices within the same dimension. For example, a row index such as `c(1, -2)` is not a meaningful request to both select row 1 and omit row 2, and R reports an error. When the desired result is clearer as a keep-list, write positive indices. When it is clearer as a removal, use negative indices.

## A deletion can drop a dimension

R matrices are vectors with a `dim` attribute. By default, subsetting a matrix may remove a dimension whose length is one. If a selection leaves one column, the result is often a vector, not a one-column matrix. The values may look right while downstream code that expects two dimensions fails.

Use `drop = FALSE` when the shape is part of the contract. For instance, `M[-4, -1, drop = FALSE]` preserves a matrix even if only one row or one column remains. The source section illustrates the same underlying behavior by noting that one of its deletions returns a standalone vector. Its point is not that a vector is an error, but that the result's structure changes and should be checked.

A quick check with `dim(result)` reveals the dimensions of a matrix. For results that might have been simplified, inspect `str(result)` or explicitly request `drop = FALSE`. This is especially valuable in functions that handle different-sized datasets, because a subset with many columns today might have just one tomorrow.

## Worked example: remove a flagged sensor and reorder channels

Suppose rows represent three sensor visits and columns represent temperature, pressure, and humidity. Define the matrix in row-wise order, so each consecutive group of three numbers fills one row:

```r
readings <- matrix(
  c(18, 101, 40,
    20,  99, 42,
    17, 103, 38),
  nrow = 3,
  byrow = TRUE,
  dimnames = list(c("visit_A", "visit_B", "visit_C"),
                  c("temp", "pressure", "humidity"))
)
```

The resulting rows are `(18, 101, 40)`, `(20, 99, 42)`, and `(17, 103, 38)`. If visit B is excluded and the channels should appear as humidity followed by temperature, use:

```r
kept <- readings[-2, c(3, 1), drop = FALSE]
```

The calculation is direct: remove row 2, then take columns 3 and 1 in that order. The result is a 2-by-2 matrix:

```text
         humidity temp
visit_A        40   18
visit_C        38   17
```

Now suppose an instrument check determines that the two retained temperature readings should be set to 19 and 18.5, in row order. Assign into the selected column:

```r
kept[, "temp"] <- c(19, 18.5)
```

The updated temperature values are 19 for visit A and 18.5 for visit C. To verify the numerical change, their mean is `(19 + 18.5) / 2 = 18.75`. This is arithmetic on the edited data, not evidence that the instrument correction is scientifically justified. In a real analysis, preserve the original values and document the rule that authorized a correction.

The example also shows why names help. `kept[, "temp"]` states which variable is changing, while `kept[, 2]` depends on remembering that temperature is currently the second column after reordering. Names make intent easier to review, though the underlying matrix still uses positions for its storage and indexing behavior.

## Assignment follows column-major order

An assignment such as `M[row_index, column_index] <- values` fills the selected cells in column-major order. R visits selected rows in their listed order for the first selected column, then moves to the next selected column. The order in which columns are specified therefore matters.

For example, in a 3-by-3 matrix, the selection `M[c(1, 3), c(2, 1)]` visits `(1,2)`, `(3,2)`, `(1,1)`, and `(3,1)`. Assigning `c(10, 20, 30, 40)` puts 10 and 20 in column 2 at rows 1 and 3, then 30 and 40 in column 1 at rows 1 and 3.

Replacement values may have length one, the same length as the selected cells, or a length that evenly divides the number of selected cells. A single value is repeated across the selection. A shorter compatible vector is recycled. For example, assigning `c(-1, 1)` to four selected cells gives `-1, 1, -1, 1` in traversal order. If the replacement length does not divide the number of target cells, R can warn about a partial recycling condition. Avoid relying on that behavior: make the intended lengths explicit and check them.

The source section presents these assignment rules, including column-wise filling and recycling. A practical extension is to test a complicated index expression on a small matrix first, or to calculate the target positions separately. That simple habit catches mistakes before a large result is modified.

## Replacing a row, column, or diagonal

To replace an entire row, supply a vector with one value per column, for example `M[2, ] <- c(5, 6, 7)`. To replace a column, supply one value per row: `M[, 1] <- c(8, 9, 10)`. In both cases, ensure the values align with the intended order. Named values can help a human reader, but ordinary numeric matrix assignment is position-based.

For a square matrix, `diag(M) <- c(1, 2, 3)` replaces the diagonal entries from top left to bottom right. The diagonal is often meaningful in covariance, distance, and adjacency matrices, so a concise diagonal assignment can be both readable and risky if the replacement values are not checked. Confirm the matrix dimensions and the intended meaning before changing it.

A subtle example occurs when copying from one slice to another, such as `M[, 3] <- M[3, ]`. The right side is selected first, then its values are assigned to the left-side positions in order. If the source and destination have different conceptual roles, matching lengths alone does not establish that the operation is sensible. Verify the mapping, not just the dimensions.

## Applications in analysis and reproducible computing

Omission is useful when creating a view of a matrix for a defined analysis: excluding a known calibration row, dropping a derived variable, or arranging channels in a plotting order. Overwriting is appropriate for deliberate transformations such as setting a documented baseline, applying a unit conversion, or replacing a diagonal with a specified value.

For scientific work, distinguish editing from filtering. A temporary subset can be useful for a calculation, but deleting observations should follow a stated rule, not an inconvenient result. Keep raw inputs immutable where possible. Build a derived object, record the selection criteria, and retain checks such as the number of rows before and after omission. For replacements, record the target coordinates or names, the replacement rule, and whether recycling was intended.

Matrices are appropriate when entries share a common atomic type. If columns contain different kinds of data, such as a date, a sample identifier, and a measured concentration, a data frame or another structured table is usually a better fit. Converting such data into a matrix can coerce values to a common type and undermine the meaning of the columns.

## Pitfalls worth checking

- **Unexpected vector output:** use `drop = FALSE` when matrix shape must persist.
- **Wrong order:** remember that selection order controls the output, and assignment traverses columns before moving to the next selected column.
- **Unintended recycling:** compare the replacement length to the number of target cells. Use a full-length vector when each cell needs a distinct value.
- **Position drift:** after reordering columns, numeric positions may no longer mean what they did before. Names can make code more legible.
- **Untraceable edits:** preserve the source object and explain why each change is valid.
- **Misused omission:** negative indices exclude positions; they do not express a data-quality rule by themselves.

These are not reasons to avoid bracket indexing. They are reasons to make the intended shape, order, and transformation visible in code.

## Exercises with short answers

1. Given a 4-by-3 matrix `X`, what does `X[-c(1, 4), c(3, 1)]` return?  
   **Answer:** A 2-by-2 selection from rows 2 and 3, with original column 3 first and column 1 second, unless dimension dropping applies. Here neither dimension is length one, so it remains a matrix.

2. A 2-by-4 matrix receives `X[, -2]`. How many columns remain, and what should you add if you must guarantee a matrix?  
   **Answer:** Three columns remain. Add `drop = FALSE` to preserve matrix structure in general.

3. In a 3-by-2 matrix, what is the assignment order for `X[c(1, 3), c(2, 1)] <- 1:4`?  
   **Answer:** The values go to `(1,2)`, `(3,2)`, `(1,1)`, then `(3,1)`.

Matrix editing becomes predictable once you track three things: which positions survive, the order in which positions are traversed, and whether the result must retain its dimensions. Make those choices explicit, and compact R expressions become easier to trust and easier for someone else to reproduce.

---

Published 2026-10-15.

**Source:** The book of R - a first course in programming and statistics, section **3.2.2 Omitting and Overwriting**.

This is an original explanatory note; the source book is not redistributed.
