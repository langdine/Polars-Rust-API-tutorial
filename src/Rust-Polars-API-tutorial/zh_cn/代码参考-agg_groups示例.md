# agg_groups示例

agg_groups将组内的行索引打包进list中。

```rust
use polars::prelude::*;
let df = df![
    "A" => ["foo", "foo", "foo", "bar", "bar", "bar"],
    "B" => ["one", "one", "two", "two", "one", "one"]]?;
let imploded = df.lazy().group_by([cols(["A","B"])])
.agg([col("A").agg_groups().alias("agg_groups")]).collect()?; 
//    ^^^^^^^ agg_groups只需要返回组内元素索引。所以列值无关，任意列都可以。
println!("{:?}",imploded);
```

Output

```bash
shape: (4, 3)
┌─────┬─────┬────────────┐
│ A   ┆ B   ┆ agg_groups │
│ --- ┆ --- ┆ ---        │
│ str ┆ str ┆ list[u32]  │
╞═════╪═════╪════════════╡
│ bar ┆ two ┆ [3]        │
│ bar ┆ one ┆ [4, 5]     │
│ foo ┆ one ┆ [0, 1]     │
│ foo ┆ two ┆ [2]        │
└─────┴─────┴────────────┘
```