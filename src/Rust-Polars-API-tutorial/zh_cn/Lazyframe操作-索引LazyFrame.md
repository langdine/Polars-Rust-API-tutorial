# 索引LazyFrame

LazyFrame的索引需要使用表达式语法。

## 列索引

在select上下文中，可以使用col()表达式选择某些列。
cols(["date", "logged_at"])，选中指定的names。col("*")或all() 表示选中所有列。exclude(["logged_at", "index"])表示排除指定列。*可用于通配符。

```rust
//单列选择
let out = types_df.clone().lazy().select([col("place")]).collect()?;

//通配符选取，选中列名以a开始的所有列
let out = types_df.clone().lazy().select([col("a*")]).collect()?;

//正则表达式
let out = types_df.clone().lazy().select([col("^.*(as|sa).*$")]).collect()?;

//多个列名选取
let out = types_df.clone().lazy().select([cols(["date", "logged_at"])]).collect()?;
//数据类型选取,选择满足数据类型的所有列。
let out = types_dfclone().lazy()
    .select([dtype_cols([DataType::Int64, DataType::UInt32, DataType::Boolean]).n_unique()])
.collect()?;
// alias重命名字段
let df_alias = df.clone().lazy()
.select([ (col("nrs") + lit(5)).alias("nrs + 5"),
(col("nrs") - lit(5)).alias("nrs - 5")])
.collect()?;
```

## 行索引

### 调用lazyframe的方法

```rust
let res : LazyFrame=types_df.clone().lazy().filter(col("id").gt_eq(lit(4)));
// filter的参数为某个返回bool数组的表达式，值为false的行将被丢弃。
println!("{}",res.collect()?);
let res:LazyFrame=types_df.clone().lazy().slice(1, 2);//返回包含切片指定的行,offset为负数表示从末尾开始计数。.slice(-5,3) 表示从倒数第5个元素开始连续选择3个元素。
```

### 在select上下文中，使用表达式进行行索引

```rust
let res:LazyFrame= employee_df.clone().lazy().select(
		col("*").filter(
			col("值").gt_eq(lit(4))
			)
		);
let res:LazyFrame= employee_df.clone().lazy().select(col("*").slice(2,3));
```
