# 重命名alias

## alias

alias用于更改单个字段名

```rust
let df_alias = df.clone().lazy()
.select([ (col("nrs") + lit(5)).alias("nrs + 5"),
(col("nrs") - lit(5)).alias("nrs - 5")])
.collect()?;
```

## name

name()返回ExprNameNameSpace，一种可以操作多个字段名的类型。

方法列表|含义
--|--
keep()|使用原始字段名。即使利用alias设置了字段名，keep将确保使用原始字段名。<br>`col("*").alias("foo").name().keep()`
map(F)|使用自定义函数返回字段名，输入参数为原始字段名。<br>F满足`Fn(&PlSmallStr) -> Result<PlSmallStr, PolarsError>`
prefix(prefix: &str)|新字段名为原始字段名加前缀，prefix和suffix无法同时使用。此时考虑使用map应用自定义函数
suffix(prefix: &str)|新字段名为原始字段名加后缀.prefix和suffix无法同时使用。此时考虑使用map应用自定义函数
to_lowercase()|小写字母
to_uppercase()|大写字母
