# 条件运算 when then otherwise

when语句能根据输入参数的不同返回不同值。

我们使用when表达式根据不同条件返回不同结果。

```rust
use polars::prelude::*;
let df = df!( "id" => &[1u32,2,3],
"value" => ["one","two","three"]).unwrap();
let res = df.lazy().select([col("id"),
when(col("id").eq(lit(1u32)))
        .then(lit("一"))//映射为字面值
        .when(col("id").eq(lit(2u32))) 
        .then(col("value"))  //映射为另一个字段的值
    .when(col("id")==lit(3u32)) //务必使用表达式方法进行比较，而不是基础运算符。 基础运算符会导致比较为false。基础运算符会导致Rust编译器比较两个表达式对象的底层内存，而不是由Polars表达式执行器执行比较。
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
