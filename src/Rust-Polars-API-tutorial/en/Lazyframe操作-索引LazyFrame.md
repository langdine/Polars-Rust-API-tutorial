# Indexing LazyFrame

Indexing in LazyFrame requires using expression syntax.

## Column Indexing

In the `select` context, you can use the `col()` expression to select certain columns. `cols(["date", "logged_at"])` selects specified names. `col("*")` or `all()` selects all columns. `exclude(["logged_at", "index"])` excludes specified columns. `*` can be used as a wildcard.

```rust
// Single column selection
let out = types_df.clone().lazy().select([col("place")]).collect()?;
// Wildcard selection, selects all columns starting with 'a'
let out = types_df.clone().lazy().select([col("a*")]).collect()?;
// Regular expression
let out = types_df.clone().lazy().select([col("^.*(as|sa).*$")]).collect()?;
// Multiple column names selection
let out = types_df.clone().lazy().select([cols(["date", "logged_at"])]).collect()?;
// Data type selection, selects all columns that match the data types.
let out = types_df.clone().lazy()
    .select([dtype_cols([DataType::Int64, DataType::UInt32, DataType::Boolean]).n_unique()])
.collect()?;
// Alias to rename fields
let df_alias = df.clone().lazy()
.select([ (col("nrs") + lit(5)).alias("nrs + 5"),
(col("nrs") - lit(5)).alias("nrs - 5")])
.collect()?;
```

## Row Indexing

### Using LazyFrame Methods

```rust
let res: LazyFrame = types_df.clone().lazy().filter(col("id").gt_eq(lit(4)));
// The filter parameter is an expression that returns a boolean array, rows with false values will be discarded.
println!("{}", res.collect()?);
let res: LazyFrame = types_df.clone().lazy().slice(1, 2); // Returns rows specified by the slice, a negative offset means counting from the end. .slice(-5,3) means selecting 3 elements starting from the 5th last element.
```

### Using Expressions for Row Indexing in Context

```rust
let res: LazyFrame = employee_df.clone().lazy().select(
        col("*").filter(
            col("value").gt_eq(lit(4))
            )
        );
let res: LazyFrame = employee_df.clone().lazy().select(col("*").slice(2,3));
```

In these examples, you can see how to use expressions to filter and slice rows and columns in a LazyFrame, providing flexible ways to manipulate data.