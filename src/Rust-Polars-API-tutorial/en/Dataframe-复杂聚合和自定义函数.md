# Complex Aggregation and Custom Functions

- Simple Aggregation: Takes a single Series as input and outputs a Series with only one element.
- Complex Aggregation: Takes multiple Series as input and outputs multiple rows and columns.

Complex aggregation requires custom functions to compute the desired results.

## DataFrame Complex Aggregation

`DataFrame.group_by(["date"])?.apply(F)` can be used to perform complex aggregation. 

F is a custom function that must satisfy `|x: DataFrame| -> Result<DataFrame, PolarsError>`. The grouped data is encapsulated into a DataFrame, allowing access to all fields. The function returns a DataFrame, and each group can return multiple rows and columns, but the order, names, and types of fields in the returned DataFrame must be consistent across different groups. You need to maintain the group field in the returned DataFrame.

```rust
let mut employee_df: DataFrame = df!(
    "Name" => ["Lao Li", "Lao Li", "Lao Li", "Lao Li", "Lao Zhang", "Lao Zhang", "Lao Zhang", "Lao Zhang", "Lao Wang", "Lao Wang", "Lao Wang", "Lao Wang"],
    "employee_ID" => ["Employee01", "Employee01", "Employee01", "Employee01", "Employee02", "Employee02", "Employee02", "Employee02", "Employee03", "Employee03", "Employee03", "Employee03"],
    "date" => ["August", "September", "October", "November", "August", "September", "October", "November", "August", "September", "October", "November"],
    "score" => [83, 24, 86, 74, 89, 59, 48, 79, 51, 71, 44, 90]
)?;

let f = |x: DataFrame| -> Result<DataFrame, PolarsError> {
    let col1: &Series = x.column("Name")?;
    let col2: &Series = x.column("employee_ID")?;
    let col3: &Series = x.column("score")?;
    let group_id = x.column("date")?.str()?.get(0).unwrap();
    // do something; We get those results below;
    let group_field = Series::new("group".into(), vec![group_id, group_id, group_id]);
    let res_field1 = Series::new("field1".into(), vec!["a1,1", "a2,1", "a3,1"]);
    let res_field2 = Series::new("field2".into(), vec!["a1,2", "a2,2", "a3,2"]);
    let res_field3 = Series::new("field3".into(), vec!["a1,3", "a2,3", "a3,3"]);
    let result = DataFrame::new(vec![group_field, res_field1, res_field2, res_field3])?;
    return Ok(result);
};

let res = employee_df.group_by(["date"])?.apply(f)?; // The aggregation returns 3 rows and 3 columns. For different groups, the schema must be consistent (field order, field count, and type).
println!("{}", res);
```

Output

```term
shape: (12, 4)
┌───────────┬────────┬────────┬────────┐
│ group     ┆ field1 ┆ field2 ┆ field3 │
│ ---       ┆ ---    ┆ ---    ┆ ---    │
│ str       ┆ str    ┆ str    ┆ str    │
╞═══════════╪════════╪════════╪════════╡
│ August    ┆ a1,1   ┆ a1,2   ┆ a1,3   │
│ August    ┆ a2,1   ┆ a2,2   ┆ a2,3   │
│ August    ┆ a3,1   ┆ a3,2   ┆ a3,3   │
│ November  ┆ a1,1   ┆ a1,2   ┆ a1,3   │
│ November  ┆ a2,1   ┆ a2,2   ┆ a2,3   │
│ …         ┆ …      ┆ …      ┆ …      │
│ September ┆ a2,1   ┆ a2,2   ┆ a2,3   │
│ September ┆ a3,1   ┆ a3,2   ┆ a3,3   │
│ October   ┆ a1,1   ┆ a1,2   ┆ a1,3   │
│ October   ┆ a2,1   ┆ a2,2   ┆ a2,3   │
│ October   ┆ a3,1   ┆ a3,2   ┆ a3,3   │
└───────────┴────────┴────────┴────────┘
```
