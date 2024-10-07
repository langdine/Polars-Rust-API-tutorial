# Expressions

Polars has a powerful concept called expressions (of type `Expr`). Polars expressions can be used in various contexts and essentially perform the function `Fn(Series) -> Series`. An `Expr` takes a `Series` as input and outputs a `Series`, allowing for chained calls.

```rust
col("foo").sort().head(2)
```

The above snippet selects the column "foo", sorts it, and then takes the first two values from the sorted output. The power of expressions lies in the fact that each expression generates a new expression, and they can be chained, stored in variables, or passed as parameters. You can run expressions through Polars' execution context. Here, we run two expressions in the `select` context:

```rust
df.lazy()
  .select([
    col("foo").sort(Default::default()).head(None),
    col("bar").filter(col("foo").eq(lit(1))).sum(),
  ])
.collect()?;
```

Each independent Polars expression can run independently without needing the results of other expressions or interacting with them. Therefore, Polars may assign expressions to different threads or processors for simultaneous execution. Polars expressions are highly parallel. Understanding Polars expressions is a key step in learning Polars.

# Contexts

Functions that can accept expressions are called contexts, and they include the following three types:

Meaning | Code
--|--
Selection | `df.select([..])`
Group Aggregation | `df.groupby(..).agg([..])`
Horizontal Stacking (hstack) or Adding Columns | `df.with_columns([..])`
