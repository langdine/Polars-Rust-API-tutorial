# Conditional Operations
The `when` statement can return different values based on different conditions. We use the `when` expression to return different results based on different conditions.

```rust
use polars::prelude::*;
let df = df!( "id" => &[1u32,2,3],
"value" => ["one","two","three"]).unwrap();
let res = df.lazy().select([col("id"),
when(col("id").eq(lit(1u32)))
        .then(lit("一")) // Map to a literal value
        .when(col("id").eq(lit(2u32)))
        .then(col("value"))  // Map to the value of another field
    .when(col("id")==(lit(3u32))) // Be sure to use expression methods for comparison, not basic operators( ==,>,<). Basic operators will result in the comparison being false. Basic operators cause the Rust compiler to compare the underlying memory of two expression objects, rather than having the comparison executed by the Polars expression executor.
        .then(lit("三"))
        .otherwise(lit("error")).alias("id_cn")
    ]).collect();
println!("{:?}",res);
```

Output
```bash
shape: (3, 2)
┌─────┬───────┐
│ id  ┆ id_cn │
│ --- ┆ ---   │
│ u32 ┆ str   │
╞═════╪═══════╡
│ 1   ┆ 一    │
│ 2   ┆ two   │
│ 3   ┆ error │
└─────┴───────┘
```