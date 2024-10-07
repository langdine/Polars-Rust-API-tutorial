# agg_groups Example

agg_groups packages the row indices within a group into a list.

```rust
use polars::prelude::*;
let df = df![
    "A" => ["foo", "foo", "foo", "bar", "bar", "bar"],
    "B" => ["one", "one", "two", "two", "one", "one"]]?;
let imploded = df.lazy().group_by([cols(["A","B"])])
.agg([col("A").agg_groups().alias("agg_groups")]).collect()?;
//    ^^^^^^^ agg_groups only needs to return the indices of elements within the group. Therefore, the column values are irrelevant, and any column can be used.
println!("{:?}",imploded);
```

Output

```bash
shape: (4, 3)
┌─────┬─────┬────────────┐
│ A   ┆ B   ┆ agg_groups │
│ --- ┆ --- ┆ ---        │
│ str ┆ str ┆ list[u32]  │
╞═════╪═════╪════════════╡
│ bar ┆ two ┆ [3]        │
│ bar ┆ one ┆ [4, 5]     │
│ foo ┆ one ┆ [0, 1]     │
│ foo ┆ two ┆ [2]        │
└─────┴─────┴────────────┘
```
