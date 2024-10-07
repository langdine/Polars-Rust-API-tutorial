# 复杂聚合和自定义函数

- 简单聚合：输入单个Series，输出只有一个元素的Series。
- 复杂聚合：输入多个Series，输出多行和多列。

复杂聚合需要自定义函数计算出我们想要的结果。

## DataFrame复杂聚合

`DataFrame.group_by(["date"])?.apply(F)` 组合可完成复杂聚合。
F为自定义函数，要求F满足 `|x: DataFrame| -> Result<DataFrame, PolarsError>`
以分组后的数据封装进Dataframe，这样可以获得所有字段，返回Dataframe，每一组可以返回多行多列，但要求不同组返回的Dataframe的字段顺序、名称、类型一致。在返回的Dataframe中需要自己维护组别字段。

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

let res = employee_df.group_by(["date"])?.apply(f)?;//一次聚合返回了3行3列，要求是对于不同组schema必须一致（字段顺序，名称，类型）
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