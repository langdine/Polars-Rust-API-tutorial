# Null, None, and NaN

In Polars, missing data is represented by Null because Polars adheres to the data specifications of the Apache Arrow project. In Rust, Option::None is used to represent missing data. So that a None value in Rust will present as null in Polars.

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

For floating-point numbers, there is also NaN (Not a Number), which is a special number usually resulting from erroneous mathematical operations. NaN is a special value of the float type and is not used to represent missing data. This means that null_count() only counts null values and does not include NaN values. The functions fill_nan and fill_null are used to fill NaN and null values, respectively.

Meaning | Expression | Return Value
--|--|--
Zero divided by zero | 0/0 | NaN
Square root of a negative number | (-1f32).sqrt() | NaN
Certain operations involving infinity<br>let inf=std::f32::INFINITY | inf*0<br>inf/inf | NaN
All mathematical operations involving NaN | NaN+1<br>NaN*1 | NaN