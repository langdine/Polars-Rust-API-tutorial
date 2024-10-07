# 索引Dataframe

## 列索引

```rust
let res: &Series =employee_df.column("员工")?;  //根据单个列名返回Series引用
let res: &Series =employee_df.select_at_idx(1)?;  //根据索引返回对应Series的引用
let res:Dataframe=employee_df.select(["类别", "薪水"])？//复制对应字段，返回新Dataframe。

let sv: Vec<&Series> = employee_df.columns(["类别", "薪水"])?;//根据多个列名返回对应Series的引用
let res: &[Series]=employee_df.get_columns()?;//返回所有列的切片，指向Dataframe内部数据结构并没有复制数据。
let res: Vec<Series>=employee_df.take_columns();//获得所有列的所有权
```

## 行索引

### Bool索引

```rust
let se=employee_df.column("业绩")?;
let mask = se.gt_eq(80)?; //判断se是否大于等于80。返回Bool数组。
let res:DataFrame = employee_df.filter(&mask)?;
```

### 切片索引

```rust
let res:DataFrame = employee_df.slice(5,3);
```

### 索引值

```rust
let idx = IdxCa::new("idx".into(), [0, 1, 9]);
let res:DataFrame =employee_df.take(&idx)?;//返回索引为0 1 9 的行
```
