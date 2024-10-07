# Null和None和NaN

在polars内部缺失数据用Null表示，这是因为polars遵守Apache Arrow
项目的数据规范。在rust中Option::None用于表示缺失数据。因此Rust的None会被Polars储存为null。

```rust
let df = df! (
    "value" => &[Some(1), None],
)?;
println!("{}", &df);
```

The output:

```bash
shape: (2, 1)
┌───────┐
│ value │
│ ---   │
│ i64   │
╞═══════╡
│ 1     │
│ null  │
└───────┘
```

对于浮点数，也存在NaN（Not a Number），是特殊的一个数，他通常由错误的数学运算产生，NaN也是float类型的特殊值，它并不用来表示缺失。这意味着null_count()只计算null值，而不包括NaN值。fill_nan、fill_null分别用于填充nan和null值。

含义|表达式|返回值
--|--|--
零除以零|0/0|NaN
对负数开根号|(-1f32).sqrt()|NaN
无穷参与的部分运算<br>let inf=std::f32::INFINITY |inf*0<br>inf/inf|NaN
NaN参与的所有数学运算|NaN+1<br>NaN*1|NaN
