# map Example

The map function applies a custom function to each column. Note that map does not consider groups. If you need to consider groups, you should use apply, as seen in [Grouping and Aggregation](Lazyframe操作-分组和聚合.md).

```rust
let df = df![
    "C" => [11, 13, 12, 10, 15, 14],
    "A" => [21, 23, 22, 20, 25, 24],
    "B" => [31, 33, 32, 30, 35, 34],
]?;
let a = SpecialEq::<Arc::<dyn FunctionOutputField>>::from_type(DataType::Int32);
let f = |x| { Ok(Some(x + 1)) };
// Since Rust is statically compiled, the return type must be known at compile time. The second parameter of the map function is used to indicate the return type.
let imploded = df.lazy().select([col("*").map(f, a).name().prefix("map_")]).collect();
```
