# Constructing a DataFrame

## Empty DataFrame

```rust
use polars::prelude::*;
let df = DataFrame::default();
```

## Creating a DataFrame from a Macro

```rust
use polars::prelude::*;
let mut arr = [0f64; 5];
let v = vec![1, 2, 3, 4, 5];
// df macro support
let df = df! (
    "nrs" => &[Some(1), Some(2), Some(3), None, Some(5)], // Direct literals, use None to represent null.
    "names" => &["A", "A", "B", "C", "B"], // Direct literals
    "col3" => &arr, // Rust array
    "groups" => &v,  // Generated from Vec
)?;
println!("{}", &df);
```

## Creating a DataFrame from Vec\<Series>

```rust
use polars::prelude::*;
let s1 = Series::new("Fruit".into(), ["Apple", "Apple", "Pear"]);
let s2 = Series::new("Color".into(), ["Red", "Yellow", "Green"]);
// s1 and s2 must have the same length.
let df: PolarsResult<DataFrame> = DataFrame::new(vec![s1, s2]);
```
