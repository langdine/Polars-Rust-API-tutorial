# 构造Dataframe

## 空白Dataframe

```rust
use polars::prelude::*;
let df = DataFrame::default();
```

## 从宏创建Dataframe

```rust
use polars::prelude::*;
let mut arr = [0f64; 5];
let v=vec![1,2,3,4,5];
//df宏支持
let df = df! (
    "nrs" => &[Some(1), Some(2), Some(3), None, Some(5)], //直接字面值，用None表示null。
    "names" => &["A", "A", "B", "C", "B"], //直接字面值
    "col3" => &arr, //rust数组
    "groups" => &v,  // 由Vec 生成
)?;
println!("{}", &df);
```

## 从Vec\<Series>构建Dataframe

```rust
use polars::prelude::*;
let s1 = Series::new("Fruit".into(), ["Apple", "Apple", "Pear"]);
let s2 = Series::new("Color".into(), ["Red", "Yellow", "Green"]);
//s1 s2必须拥有同样的长度。
let df: PolarsResult<DataFrame> = DataFrame::new(vec![s1, s2]);
```
