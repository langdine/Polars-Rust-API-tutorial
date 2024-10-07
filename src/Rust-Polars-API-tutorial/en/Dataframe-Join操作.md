# Join Operations

## What is a join?

In data processing, the following requirement often arises: one table stores a large number of user IDs along with user names, ages, phone numbers, shopping addresses, and other user information, while another table stores user IDs along with items to be shipped, item prices, and other transaction information. How can we integrate the data from these two tables based on the ID?

The join operation is used to solve this problem. It combines two dataframes based on specified fields. A join requires two dataframes as input, referred to as the left table and the right table. Additionally, it is necessary to specify which fields are used as the basis for matching, known as "keys." All fields other than the keys are used as values, referred to as left table value fields and right table value fields, respectively. The result of a join operation includes: keys, left table value fields, and right table value fields.

There are four scenarios that can occur with a join:

1. A key appears in the left table but not in the right table; the corresponding right table value fields for this key are considered null.
2. A key appears in the right table but not in the left table; the corresponding left table value fields for this key are considered null.
3. A key appears in both tables; the key is unique in at least one of the tables.
4. A key appears in both tables; the key is not unique in either table. This results in a many-to-many join, which is often meaningless and usually indicates a data error that needs careful investigation.
**A key can be composed of multiple columns or a single column.**

The main types of join operations are divided into five categories:

1. Left Join (left_join): The result retains all keys from the left table, along with their corresponding left table value fields and right table value fields. Values corresponding to keys not appearing in the right table are considered null.
2. Right Join (right_join): The result retains all keys from the right table, along with their corresponding left table value fields and right table value fields. Values corresponding to keys not appearing in the left table are considered null.
3. Inner Join (inner_join): The result retains keys common to both the left and right tables, along with their corresponding left table value fields and right table value fields.
4. Full Join (full_join): The result retains all keys from both the left and right tables, along with their corresponding left table value fields and right table value fields. Non-existent values are considered null.
5. Cross Join (cross_join): A "Cartesian product" refers to all possible combinations of two datasets. For example, if the left table value fields for a certain key contain two rows, A and B, and the right table value fields contain two rows, 1 and 2, then the Cartesian join for this key will produce four rows: (A, 1), (A, 2), (B, 1), (B, 2).

Left join and right join are essentially the same, just swapping the left and right tables. Therefore, Polars only implements left_join. Dataframes and lazyframes have different syntax.

## Join API

```rust
left_df.left_join(right_df, left_on, right_on)
left_df.inner_join(right_df, left_on, right_on)
left_df.full_join(right_df, left_on, right_on)
left_df.cross_join(right_df, left_on, right_on)
// Usage:
left.full_join(right, ["join_column_left"], ["join_column_right"])
```

Exp:

The `employee_score` table stores employee performance data, while the `employee_info` table stores employee identity information. We will merge the two DataFrame using the `employee_ID` field as the key.

```rust
let mut employee_score: DataFrame = df!(
    "employee_ID" => ["Employee01", "Employee01", "Employee01", "Employee01", "Employee02", "Employee02", "Employee02", "Employee02", "Employee03", "Employee03", "Employee03", "Employee03"],
    "date" => ["August", "September", "October", "November", "August", "September", "October", "November", "August", "September", "October", "November"],
    "score" => [83, 24, 86, 74, 89, 59, 48, 79, 51, 71, 44, 90]
    )?;
let mut employee_info = df!{
    "Name" => ["Lao Li", "Lao Zhang", "Lao Wang"],
    "employee_ID" => ["Employee01", "Employee02", "Employee03"],
    "email" => ["zdl361@126.com","LaoZhang@gmail.com","LaoWang@hotmail.com"]
    }?;

let res = employee_score.left_join(&employee_info, ["employee_ID"], ["employee_ID"])?;
println!("employee_score:{}\nemployee_info:{}\nafter join:{}",employee_score,employee_info,res);
```

Output

```term
employee_score:shape: (12, 3)
┌─────────────┬───────────┬───────┐
│ employee_ID ┆ date      ┆ score │
│ ---         ┆ ---       ┆ ---   │
│ str         ┆ str       ┆ i32   │
╞═════════════╪═══════════╪═══════╡
│ Employee01  ┆ August    ┆ 83    │
│ Employee01  ┆ September ┆ 24    │
│ Employee01  ┆ October   ┆ 86    │
│ Employee01  ┆ November  ┆ 74    │
│ Employee02  ┆ August    ┆ 89    │
│ …           ┆ …         ┆ …     │
│ Employee02  ┆ November  ┆ 79    │
│ Employee03  ┆ August    ┆ 51    │
│ Employee03  ┆ September ┆ 71    │
│ Employee03  ┆ October   ┆ 44    │
│ Employee03  ┆ November  ┆ 90    │
└─────────────┴───────────┴───────┘
employee_info:shape: (3, 3)
┌───────────┬─────────────┬─────────────────────┐
│ Name      ┆ employee_ID ┆ email               │
│ ---       ┆ ---         ┆ ---                 │
│ str       ┆ str         ┆ str                 │
╞═══════════╪═════════════╪═════════════════════╡
│ Lao Li    ┆ Employee01  ┆ zdl361@126.com      │
│ Lao Zhang ┆ Employee02  ┆ LaoZhang@gmail.com  │
│ Lao Wang  ┆ Employee03  ┆ LaoWang@hotmail.com │
└───────────┴─────────────┴─────────────────────┘
after join:shape: (12, 5)
┌─────────────┬───────────┬───────┬───────────┬─────────────────────┐
│ employee_ID ┆ date      ┆ score ┆ Name      ┆ email               │
│ ---         ┆ ---       ┆ ---   ┆ ---       ┆ ---                 │
│ str         ┆ str       ┆ i32   ┆ str       ┆ str                 │
╞═════════════╪═══════════╪═══════╪═══════════╪═════════════════════╡
│ Employee01  ┆ August    ┆ 83    ┆ Lao Li    ┆ zdl361@126.com      │
│ Employee01  ┆ September ┆ 24    ┆ Lao Li    ┆ zdl361@126.com      │
│ Employee01  ┆ October   ┆ 86    ┆ Lao Li    ┆ zdl361@126.com      │
│ Employee01  ┆ November  ┆ 74    ┆ Lao Li    ┆ zdl361@126.com      │
│ Employee02  ┆ August    ┆ 89    ┆ Lao Zhang ┆ LaoZhang@gmail.com  │
│ …           ┆ …         ┆ …     ┆ …         ┆ …                   │
│ Employee02  ┆ November  ┆ 79    ┆ Lao Zhang ┆ LaoZhang@gmail.com  │
│ Employee03  ┆ August    ┆ 51    ┆ Lao Wang  ┆ LaoWang@hotmail.com │
│ Employee03  ┆ September ┆ 71    ┆ Lao Wang  ┆ LaoWang@hotmail.com │
│ Employee03  ┆ October   ┆ 44    ┆ Lao Wang  ┆ LaoWang@hotmail.com │
│ Employee03  ┆ November  ┆ 90    ┆ Lao Wang  ┆ LaoWang@hotmail.com │
└─────────────┴───────────┴───────┴───────────┴─────────────────────┘
```