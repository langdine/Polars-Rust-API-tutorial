# over Example
The over function specifies grouping columns within an expression, allowing you to perform operations without applying group_by beforehand. The expression is applied within the group, and the aggregation function returns a value for each element within the group. The effect of over is equivalent to group_by + aggregation + join.

```rust
use polars::prelude::*;
fn main() -> Result<(), PolarsError> {
    let df = df![
        "Category" => ["Development", "Development", "Development", "Development", "Development", "Intern", "Intern", "Sales", "Sales", "Sales"],
        "EmployeeID" => [11, 7, 9, 8, 10, 5, 2, 3, 1, 4],
        "Salary" => [5200, 4200, 4500, 6000, 5200, 3500, 3900, 4800, 5000, 4800],
    ]?;
    // We have a list of employee salaries and we want to understand the salary ranking of employees in various positions.
    let imploded = df.lazy()
        .select([
            col("*"),
            col("Salary").mean().alias("Average Salary").over([col("Category")]),
            col("Salary").rank(RankOptions::default(), None).over([col("Category")]).alias("Salary Rank Over Category"),
            col("Salary").rank(RankOptions::default(), None).alias("Salary Rank")
        ]);
        println!("{:?}",imploded);
        Ok(())
}
```

Output

```bash
shape: (10, 6)
┌─────────────┬────────────┬────────┬────────────────┬───────────────────────────┬─────────────┐
│ Category    ┆ EmployeeID ┆ Salary ┆ Average Salary ┆ Salary Rank Over Category ┆ Salary Rank │
│ ---         ┆ ---        ┆ ---    ┆ ---            ┆ ---                       ┆ ---         │
│ str         ┆ i32        ┆ i32    ┆ f64            ┆ u32                       ┆ u32         │
╞═════════════╪════════════╪════════╪════════════════╪═══════════════════════════╪═════════════╡
│ Development ┆ 11         ┆ 5200   ┆ 5020.0         ┆ 3                         ┆ 7           │
│ Development ┆ 7          ┆ 4200   ┆ 5020.0         ┆ 1                         ┆ 3           │
│ Development ┆ 9          ┆ 4500   ┆ 5020.0         ┆ 2                         ┆ 4           │
│ Development ┆ 8          ┆ 6000   ┆ 5020.0         ┆ 4                         ┆ 8           │
│ Development ┆ 10         ┆ 5200   ┆ 5020.0         ┆ 3                         ┆ 7           │
│ Intern      ┆ 5          ┆ 3500   ┆ 3700.0         ┆ 1                         ┆ 1           │
│ Intern      ┆ 2          ┆ 3900   ┆ 3700.0         ┆ 2                         ┆ 2           │
│ Sales       ┆ 3          ┆ 4800   ┆ 4866.666667    ┆ 1                         ┆ 5           │
│ Sales       ┆ 1          ┆ 5000   ┆ 4866.666667    ┆ 2                         ┆ 6           │
│ Sales       ┆ 4          ┆ 4800   ┆ 4866.666667    ┆ 1                         ┆ 5           │
└─────────────┴────────────┴────────┴────────────────┴───────────────────────────┴─────────────┘
```