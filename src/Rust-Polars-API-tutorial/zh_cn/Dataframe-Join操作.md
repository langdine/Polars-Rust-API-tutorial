# Join操作

## 什么是join联接

数据处理中经常出现以下需求：一张表保存了大量用户id和用户姓名、年龄、手机号、购物地址等等用户信息，另一张表保存了用户id和待快递商品，商品价格等等交易信息。如何能根据id将两张表的数据整合到一起。

联接操作用于解决以上问题。根据两张dataframe指定字段，将两张表组合到一起。join需要两个dataframe作为输入，分别称为左表和右表；另外必须指明哪些字段被用做匹配依据，称为"键"，除了键以外的所有字段被用作值，分别称为左表值字段、右表值字段。join运算的结果包含：键，左表值字段、右表值字段。
join会出现4种情况

1. 某键在左表中出现，在右表未出现；此键对应的右表值字段视为null
2. 某键在右表中出现，在左表未出现；此键对应的左表值字段视为null
3. 某键在两张表中都出现; 键至少在其中一个表是unique的。
4. 某键在两张表中都出现; 键在两张表都不是unique的。会导致多对多联接，结果意义不明，通常是数据错误导致，需仔细排查。

可以同时使用多个字段作为键。

主要链接操作分5种：

1. 左联接left_join:结果保留所有左表的键，及其对应的左表值字段、右表值字段。对于右表中未出现的键对应的值视为null。
2. 右联接right_join: 结果保留所有右表的键，及其对应的左表值字段、右表值字段。对于左表中未出现的键对应的值视为null。
3. 内联接inner_join:结果保留左表和右表共有的键，及其对应的左表值字段、右表值字段。
4. 全联接full_join:结果保留左表和右表所有的键，及其对应的左表值字段、右表值字段，不存在的值被视为null。
5. 笛卡尔联接cross_join:"笛卡尔积"是指两个数据集的所有可能的组合。例如，对于某键的左表值字段包含A、B两行，右表值字段包含1、2两行，那么这个键的笛卡尔联接将产生四行：(A, 1)，(A, 2)，(B, 1)，(B, 2)。

left_join和right_join本质相同仅仅是调换了左表，右表，因此polars仅仅实现了left_join。
dataframe和lazyframe有不同的语法。

## joinAPI

```rust
left_df.left_join(right_df,  left_on,right_on)
left_df.inner_join(right_df,  left_on,right_on)
left_df.full_join(right_df,  left_on,right_on)
left_df.cross_join(right_df,  left_on,right_on)
//用法：
left.full_join(right, ["join_column_left"], ["join_column_right"])
```


示例:

`employee_score`储存了雇员的考核数据。`employee_info`储存了雇员的身份. 我们将使用`employee_ID`字段合并两张表.

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