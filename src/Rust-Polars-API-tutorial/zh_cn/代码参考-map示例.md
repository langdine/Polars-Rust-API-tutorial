# map示例

map将自定义函数应用于每一列，注意map不考虑组别。如果需要考虑组别应该使用apply，见[分组和聚合](Lazyframe操作-分组和聚合.md)。

```rust
let df = df![
    "C" => [11, 13, 12, 10, 15, 14],
    "A" => [21, 23, 22, 20, 25, 24],
    "B" => [31, 33, 32, 30, 35, 34],
]?;
let a= SpecialEq::<Arc::<dyn FunctionOutputField>>::from_type(DataType::Int32);
let f=|x|{Ok(Some(x+1))};
//由于rust是静态编译，因此返回值类型必须编译时可知。map函数的第二个参数用来标记返回值类型。
let imploded = df.lazy().select([col("*").map(f,a).name().prefix("map_")]).collect();
```