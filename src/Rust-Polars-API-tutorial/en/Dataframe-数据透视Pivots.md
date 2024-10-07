# Data Pivots

Data pivots transform data from a long format to a wide format and apply aggregation functions.

```rust
// Add "pivot" to the polars features in cargo.toml.
let mut employee_df: DataFrame = df!(
        "Name" => ["Lao Li", "Lao Li", "Lao Li", "Lao Li", "Lao Zhang", "Lao Zhang", "Lao Zhang", "Lao Zhang", "Lao Wang", "Lao Wang", "Lao Wang", "Lao Wang"],
        "employee_ID" => ["Employee01", "Employee01", "Employee01", "Employee01", "Employee02", "Employee02", "Employee02", "Employee02", "Employee03", "Employee03", "Employee03", "Employee03"],
        "date" => ["August", "September", "October", "November", "August", "September", "October", "November", "August", "September", "October", "November"],
        "score" => [83, 24, 86, 74, 89, 59, 48, 79, 51, 71, 44, 90]
    )?;
    
use polars_lazy::frame::pivot::pivot;
let out = pivot(&employee_df, ["date"], Some(["Name", "employee_ID"]), Some(["score"]), false, None, None)?;
println!("long format:\n{}\nwide format:\n{}", employee_df, out);
```

Output

```term
long format:
shape: (12, 4)
┌───────────┬─────────────┬───────────┬───────┐
│ Name      ┆ employee_ID ┆ date      ┆ score │
│ ---       ┆ ---         ┆ ---       ┆ ---   │
│ str       ┆ str         ┆ str       ┆ i32   │
╞═══════════╪═════════════╪═══════════╪═══════╡
│ Lao Li    ┆ Employee01  ┆ August    ┆ 83    │
│ Lao Li    ┆ Employee01  ┆ September ┆ 24    │
│ Lao Li    ┆ Employee01  ┆ October   ┆ 86    │
│ Lao Li    ┆ Employee01  ┆ November  ┆ 74    │
│ Lao Zhang ┆ Employee02  ┆ August    ┆ 89    │
│ …         ┆ …           ┆ …         ┆ …     │
│ Lao Zhang ┆ Employee02  ┆ November  ┆ 79    │
│ Lao Wang  ┆ Employee03  ┆ August    ┆ 51    │
│ Lao Wang  ┆ Employee03  ┆ September ┆ 71    │
│ Lao Wang  ┆ Employee03  ┆ October   ┆ 44    │
│ Lao Wang  ┆ Employee03  ┆ November  ┆ 90    │
└───────────┴─────────────┴───────────┴───────┘
wide format:
shape: (3, 6)
┌───────────┬─────────────┬────────┬───────────┬─────────┬──────────┐
│ Name      ┆ employee_ID ┆ August ┆ September ┆ October ┆ November │
│ ---       ┆ ---         ┆ ---    ┆ ---       ┆ ---     ┆ ---      │
│ str       ┆ str         ┆ i32    ┆ i32       ┆ i32     ┆ i32      │
╞═══════════╪═════════════╪════════╪═══════════╪═════════╪══════════╡
│ Lao Li    ┆ Employee01  ┆ 83     ┆ 24        ┆ 86      ┆ 74       │
│ Lao Zhang ┆ Employee02  ┆ 89     ┆ 59        ┆ 48      ┆ 79       │
│ Lao Wang  ┆ Employee03  ┆ 51     ┆ 71        ┆ 44      ┆ 90       │
└───────────┴─────────────┴────────┴───────────┴─────────┴──────────┘
```
