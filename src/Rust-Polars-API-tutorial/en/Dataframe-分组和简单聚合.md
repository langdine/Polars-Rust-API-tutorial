# Grouping and Simple Aggregation

LazyFrame is designed to be more user-friendly: it is recommended to convert a DataFrame to a LazyFrame using `dataframe.lazy()` before performing aggregation.

| Term          | Meaning                                                                 |
|---------------|-------------------------------------------------------------------------|
| Simple Aggregation | Input several data points and return a single value based on a certain algorithm. For example, min, max, mean, head, tail, etc., are all aggregation functions. |
| Grouping      | As the name suggests, it involves grouping data based on specified fields, and subsequent aggregation operations will call the aggregation function once for each group. |

## Practical Example 1

Observe the sample data `employee_df`, which contains the performance of 3 employees over the past 4 months.

```rust
let mut employee_df: DataFrame = df!(
    "Name" => ["Lao Li", "Lao Li", "Lao Li", "Lao Li", "Lao Zhang", "Lao Zhang", "Lao Zhang", "Lao Zhang", "Lao Wang", "Lao Wang", "Lao Wang", "Lao Wang"],
    "Employee ID" => ["Employee01", "Employee01", "Employee01", "Employee01", "Employee02", "Employee02", "Employee02", "Employee02", "Employee03", "Employee03", "Employee03", "Employee03"],
    "Date" => ["August", "September", "October", "November", "August", "September", "October", "November", "August", "September", "October", "November"],
    "Performance" => [83, 24, 86, 74, 89, 59, 48, 79, 51, 71, 44, 90]
)?;
```

The current requirement is to calculate the average performance for each person across all months and count the number of times each person's performance exceeded 70. The data needs to be grouped by `Employee ID` and then aggregated.

```rust
let res = employee_df.lazy().group_by(["Employee ID", "Name"]) // group_by may disrupt row order; group_by_stable can preserve the original row order.
    .agg([
        col("Performance").mean().alias("Average Performance"),
        col("Performance").gt(70).cast(DataType::Int32).sum().alias("Count Greater Than 70")
    ]).collect()?;
    
println!("{}", res);
```

Polars provides aggregation functions through expressions to accomplish simple aggregation.

```term
shape: (3, 4)
┌─────────────┬───────────┬─────────────────────┬───────────────────────┐
│ Employee ID ┆ Name      ┆ Average Performance ┆ Count Greater Than 70 │
│ ---         ┆ ---       ┆ ---                 ┆ ---                   │
│ str         ┆ str       ┆ f64                 ┆ i32                   │
╞═════════════╪═══════════╪═════════════════════╪═══════════════════════╡
│ Employee01  ┆ Lao Li    ┆ 66.75               ┆ 3                     │
│ Employee02  ┆ Lao Zhang ┆ 68.75               ┆ 2                     │
│ Employee03  ┆ Lao Wang  ┆ 64.0                ┆ 2                     │
└─────────────┴───────────┴─────────────────────┴───────────────────────┘
```

## Practical Example 2

Calculate the top two performers and their corresponding performances for each month.

```rust
let step1 = employee_df.lazy().group_by(["Date"]) // group_by may disrupt row order; group_by_stable can preserve the original row order.
    .agg([
        col("Employee ID"),
        col("Performance"),
        col("Performance").rank(RankOptions::default(), None).alias("rank"),
    ]);

let step2 =step1.clone().explode([col("Employee ID"), col("Performance"), col("rank")]);
let step3 = step2.clone()
    .filter(col("rank").gt_eq(2));

println!("step1:\n{:?}\nstep2:{:?}\nstep3:\n{:?}",step1.collect(),step2.collect(),step3.collect());

```

### Step 1

```rust
.group_by(["Date"]).agg([
    col("Employee ID"),
    col("Performance"),
    col("Performance").rank(RankOptions::default(), None).alias("rank"),
])
```

The `agg` aggregation operation called in Practical Example 2 wraps multiple results within a group into a list.

```term
shape: (4, 4)
┌───────────┬─────────────────────────────────┬──────────────┬───────────┐
│ Date      ┆ Employee ID                     ┆ Performance  ┆ rank      │
│ ---       ┆ ---                             ┆ ---          ┆ ---       │
│ str       ┆ list[str]                       ┆ list[i32]    ┆ list[u32] │
╞═══════════╪═════════════════════════════════╪══════════════╪═══════════╡
│ August    ┆ ["Employee01", "Employee02", "… ┆ [83, 89, 51] ┆ [2, 3, 1] │
│ October   ┆ ["Employee01", "Employee02", "… ┆ [86, 48, 44] ┆ [3, 2, 1] │
│ November  ┆ ["Employee01", "Employee02", "… ┆ [74, 79, 90] ┆ [1, 2, 3] │
│ September ┆ ["Employee01", "Employee02", "… ┆ [24, 59, 71] ┆ [1, 2, 3] │
└───────────┴─────────────────────────────────┴──────────────┴───────────┘
```

### Step 2

`.explode([col("Employee ID"), col("Performance"), col("rank")])`

This call unpacks the values wrapped in a list.

```term
shape: (12, 4)
┌───────────┬─────────────┬─────────────┬──────┐
│ Date      ┆ Employee ID ┆ Performance ┆ rank │
│ ---       ┆ ---         ┆ ---         ┆ ---  │
│ str       ┆ str         ┆ i32         ┆ u32  │
╞═══════════╪═════════════╪═════════════╪══════╡
│ September ┆ Employee01  ┆ 24          ┆ 1    │
│ September ┆ Employee02  ┆ 59          ┆ 2    │
│ September ┆ Employee03  ┆ 71          ┆ 3    │
│ November  ┆ Employee01  ┆ 74          ┆ 1    │
│ November  ┆ Employee02  ┆ 79          ┆ 2    │
│ …         ┆ …           ┆ …           ┆ …    │
│ October   ┆ Employee02  ┆ 48          ┆ 2    │
│ October   ┆ Employee03  ┆ 44          ┆ 1    │
│ August    ┆ Employee01  ┆ 83          ┆ 2    │
│ August    ┆ Employee02  ┆ 89          ┆ 3    │
│ August    ┆ Employee03  ┆ 51          ┆ 1    │
└───────────┴─────────────┴─────────────┴──────┘
```

### step3

```rust
.filter(col("rank").gt_eq(2))
```
Output:

```term
shape: (8, 4)
┌───────────┬─────────────┬─────────────┬──────┐
│ Date      ┆ Employee ID ┆ Performance ┆ rank │
│ ---       ┆ ---         ┆ ---         ┆ ---  │
│ str       ┆ str         ┆ i32         ┆ u32  │
╞═══════════╪═════════════╪═════════════╪══════╡
│ November  ┆ Employee02  ┆ 79          ┆ 2    │
│ November  ┆ Employee03  ┆ 90          ┆ 3    │
│ August    ┆ Employee01  ┆ 83          ┆ 2    │
│ August    ┆ Employee02  ┆ 89          ┆ 3    │
│ October   ┆ Employee01  ┆ 86          ┆ 3    │
│ October   ┆ Employee02  ┆ 48          ┆ 2    │
│ September ┆ Employee02  ┆ 59          ┆ 2    │
│ September ┆ Employee03  ┆ 71          ┆ 3    │
└───────────┴─────────────┴─────────────┴──────┘
```